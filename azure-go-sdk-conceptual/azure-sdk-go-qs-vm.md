---
title: "從 Go 部署 Azure 虛擬機器"
description: "使用 Azure SDK for Go 部署 Azure 虛擬機器。"
keywords: "azure, 虛擬機器, vm, go, golang, azure sdk"
author: sptramer
ms.author: sttramer
ms.date: 02/08/2018
ms.topic: quickstart
ms.devlang: go
manager: routlaw
ms.openlocfilehash: ae460dbf21b13c40f3d564274f8b790afe005aae
ms.sourcegitcommit: af3473779cd7c2978f290fbdc51ee15eb1130840
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a><span data-ttu-id="7d7fe-104">快速入門：使用 Azure SDK for Go 從範本部署 Azure 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="7d7fe-104">Quickstart: Deploy an Azure virtual machine from a template with the Azure SDK for Go</span></span>

<span data-ttu-id="7d7fe-105">本快速入門著重於使用 Azure SDK for Go 從範本部署資源。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-105">This quickstart focuses on deploying resources from a template with the Azure SDK for Go.</span></span> <span data-ttu-id="7d7fe-106">範本是 [Azure 資源群組](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)內含之所有資源的快照集。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-106">Templates are snapshots of all of the resources contained within an [Azure resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).</span></span> <span data-ttu-id="7d7fe-107">在執行有用工作的過程中，您將熟悉 SDK 的功能和慣例。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-107">Along the way, you'll become familiar with the functionality and conventions of the SDK while performing a useful task.</span></span>

<span data-ttu-id="7d7fe-108">在本快速入門結束時，您會有使用使用者名稱和密碼登入的執行中 VM。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-108">At the end of this quickstart, you have a running VM that you log into with a username and password.</span></span>

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

<span data-ttu-id="7d7fe-109">如果您採用本機安裝 Azure CLI，本快速入門會要求您執行 CLI 2.0.24 版或更新版本。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-109">If you use a local install of the Azure CLI, this quickstart requires CLI version 2.0.24 or later.</span></span> <span data-ttu-id="7d7fe-110">執行 `az --version` 以確定您的 CLI 安裝符合此需求。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-110">Run `az --version` to make sure your CLI install meets this requirement.</span></span> <span data-ttu-id="7d7fe-111">如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-111">If you need to install or upgrade, see [Install the Azure CLI 2.0](/cli/azure/install-azure-cli).</span></span>

## <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="7d7fe-112">安裝 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="7d7fe-112">Install the Azure SDK for Go</span></span> 

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a><span data-ttu-id="7d7fe-113">建立服務主體</span><span class="sxs-lookup"><span data-stu-id="7d7fe-113">Create a service principal</span></span>

<span data-ttu-id="7d7fe-114">若要使用應用程式以非互動方式登入，您需要服務主體。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-114">To log in non-interactively with an application, you need a service principal.</span></span> <span data-ttu-id="7d7fe-115">服務主體是角色型存取控制 (RBAC) 的一部分，可建立唯一的使用者身分識別。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-115">Service principals are part of role-based access control (RBAC), which creates a unique user identity.</span></span> <span data-ttu-id="7d7fe-116">若要使用 CLI 建立新的服務主體，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="7d7fe-116">To create a new service principal with the CLI, run the following command:</span></span>

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart
```

<span data-ttu-id="7d7fe-117">__務必__在輸出中記錄 `appId``password` 和 `tenant` 值。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-117">__Make sure__ to record the `appId`, `password`, and `tenant` values in the output.</span></span> <span data-ttu-id="7d7fe-118">應用程式會使用這些值來向 Azure 進行驗證。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-118">These values are used by the application to authenticate with Azure.</span></span>

<span data-ttu-id="7d7fe-119">如需使用 Azure CLI 2.0 建立和管理服務主體的詳細資訊，請參閱[使用 Azure CLI 2.0 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-119">For more information on creating and managing service principals with the Azure CLI 2.0, see [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="7d7fe-120">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="7d7fe-120">Get the code</span></span>

<span data-ttu-id="7d7fe-121">使用 `go get` 來取得快速入門程式碼及其所有相依性。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-121">Get the quickstart code and all of its dependencies with `go get`.</span></span>

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm/...
```

<span data-ttu-id="7d7fe-122">此程式碼會編譯，但是要等到您提供您的 Azure 帳戶和所建立服務主體的相關資訊，才會正確執行。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-122">This code compiles, but doesn't run correctly until you provide it information about your Azure account and the created service principal.</span></span> <span data-ttu-id="7d7fe-123">`main.go` 中有 `config` 變數，其中包含 `authInfo` 結構。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-123">In `main.go` there is a variable, `config`, which contains an `authInfo` struct.</span></span> <span data-ttu-id="7d7fe-124">此結構的欄位值必須加以取代，才能正確地驗證。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-124">This struct needs to have its field values replaced in order to authenticate correctly.</span></span>

```go
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }
```

* <span data-ttu-id="7d7fe-125">`SubscriptionID`：您的訂用帳戶識別碼，您可以使用下列 CLI 命令取得</span><span class="sxs-lookup"><span data-stu-id="7d7fe-125">`SubscriptionID`: Your subscription ID, which can be obtained from the CLI command</span></span>

  ```azurecli-interactive
  az account show --query id -o tsv
  ```

* <span data-ttu-id="7d7fe-126">`TenantID`：您的租用戶識別碼，建立服務主體時所記錄的 `tenant` 值</span><span class="sxs-lookup"><span data-stu-id="7d7fe-126">`TenantID`: Your tenant ID, the `tenant` value recorded when creating the service principal</span></span>
* <span data-ttu-id="7d7fe-127">`ServicePrincipalID`：建立服務主體時所記錄的 `appId` 值</span><span class="sxs-lookup"><span data-stu-id="7d7fe-127">`ServicePrincipalID`: The `appId` value recorded when creating the service principal</span></span>
* <span data-ttu-id="7d7fe-128">`ServicePrincipalSecret`：建立服務主體時所記錄的 `password` 值</span><span class="sxs-lookup"><span data-stu-id="7d7fe-128">`ServicePrincipalSecret`: The `password` value recorded when creating the service principal</span></span>

<span data-ttu-id="7d7fe-129">您也必須編輯 `vm-quickstart-params.json` 檔案中的值。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-129">You also need to edit a value in the `vm-quickstart-params.json` file.</span></span>

```json
    "vm_password": {
        "value": "_"
    }
```

* <span data-ttu-id="7d7fe-130">`vm_password`：VM 使用者帳戶的密碼。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-130">`vm_password`: The password for the VM user account.</span></span> <span data-ttu-id="7d7fe-131">它的長度必須為 12-72 個字元，而且包含下列 3 個字元：</span><span class="sxs-lookup"><span data-stu-id="7d7fe-131">It must be 12-72 characters in length and contain 3 of the following characters:</span></span>
  * <span data-ttu-id="7d7fe-132">小寫字母</span><span class="sxs-lookup"><span data-stu-id="7d7fe-132">A lowercase letter</span></span>
  * <span data-ttu-id="7d7fe-133">大寫字母</span><span class="sxs-lookup"><span data-stu-id="7d7fe-133">An uppercase letter</span></span>
  * <span data-ttu-id="7d7fe-134">數字</span><span class="sxs-lookup"><span data-stu-id="7d7fe-134">A number</span></span>
  * <span data-ttu-id="7d7fe-135">符號</span><span class="sxs-lookup"><span data-stu-id="7d7fe-135">A symbol</span></span>

## <a name="running-the-code"></a><span data-ttu-id="7d7fe-136">執行程式碼</span><span class="sxs-lookup"><span data-stu-id="7d7fe-136">Running the code</span></span>

<span data-ttu-id="7d7fe-137">使用 `go run` 命令執行本快速入門。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-137">Run the quickstart with the `go run` command.</span></span>

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm
go run main.go
```

<span data-ttu-id="7d7fe-138">如果部署失敗，您會收到一則訊息，指出發生問題，但沒有任何特定詳細資料。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-138">If there is a failure in the deployment, you get a message indicating that there was an issue, but without any specific details.</span></span> <span data-ttu-id="7d7fe-139">透過 Azure CLI，使用下列命令取得部署失敗的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="7d7fe-139">Using the Azure CLI, get the details of the deployment failure with the following command:</span></span>

```azurecli-interactive
az group deployment show -g GoVMQuickstart -n VMDeployQuickstart
```

<span data-ttu-id="7d7fe-140">如果部署成功，您會看到一則訊息，提供使用者名稱、IP 位址和密碼，以便登入新建立的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-140">If the deployment is successful, you see a message giving the username, IP address, and password for logging into the newly created virtual machine.</span></span> <span data-ttu-id="7d7fe-141">透過 SSH 連到這部電腦，以確認它已啟動並執行中。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-141">SSH into this machine to confirm that it is up and running.</span></span>

## <a name="cleaning-up"></a><span data-ttu-id="7d7fe-142">清除</span><span class="sxs-lookup"><span data-stu-id="7d7fe-142">Cleaning up</span></span>

<span data-ttu-id="7d7fe-143">透過 CLI 刪除資源群組，以清除您在本快速入門期間建立的資源。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-143">Clean up the resources created during this quickstart by deleting the resource group with the CLI.</span></span>

```azurecli-interactive
az group delete -n GoVMQuickstart
```

## <a name="code-in-depth"></a><span data-ttu-id="7d7fe-144">深入了解程式碼</span><span class="sxs-lookup"><span data-stu-id="7d7fe-144">Code in depth</span></span>

<span data-ttu-id="7d7fe-145">快速入門程式碼的功用可細分為一組變數和數個小型函式，我們會在此進行討論。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-145">What the quickstart code does is broken down into a block of variables and several small functions, each of which are discussed here.</span></span>

### <a name="variable-assignments-and-structs"></a><span data-ttu-id="7d7fe-146">指派變數與結構</span><span class="sxs-lookup"><span data-stu-id="7d7fe-146">Variable assignments and structs</span></span>

<span data-ttu-id="7d7fe-147">由於快速入門具有獨立性，所以使用全域變數，而不使用命令列選項或環境變數。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-147">Since quickstart is self-contained, it uses global variables rather than command-line options or environment variables.</span></span>

```go
type authInfo struct {
        TenantID               string
        SubscriptionID         string
        ServicePrincipalID     string
        ServicePrincipalSecret string
}
```

<span data-ttu-id="7d7fe-148">系統會宣告 `authInfo` 結構，以封裝使用 Azure 服務進行授權所需的全部資訊。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-148">The `authInfo` struct is declared to encapsulate all of the information needed for authorization with Azure services.</span></span>

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

var (
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }

    ctx = context.Background()

    token *adal.ServicePrincipalToken
)
```

<span data-ttu-id="7d7fe-149">系統會宣告值，以提供所建立資源的名稱。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-149">Values are declared which give the names of created resources.</span></span> <span data-ttu-id="7d7fe-150">在此也會指定位置，您可以變更位置，以查看部署在其他資料中心的行為。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-150">The location is also specified here, which you can change to see how deployments behave in other datacenters.</span></span> <span data-ttu-id="7d7fe-151">並非每個資料中心都具備所有必要的資源。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-151">Not every datacenter has all of the required resources available.</span></span>

<span data-ttu-id="7d7fe-152">`templateFile` 和 `parametersFile` 常數會指向部署所需的檔案。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-152">The `templateFile` and `parametersFile` constants point to the files needed for deployment.</span></span> <span data-ttu-id="7d7fe-153">服務主體權杖會在稍後說明，而 `ctx` 變數是網路作業的 [Go 內容](https://blog.golang.org/context)。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-153">The service principal token is covered later, and the `ctx` variable is a [Go context](https://blog.golang.org/context) for the network operations.</span></span>

### <a name="init-and-authorization"></a><span data-ttu-id="7d7fe-154">init() 和授權</span><span class="sxs-lookup"><span data-stu-id="7d7fe-154">init() and authorization</span></span>

<span data-ttu-id="7d7fe-155">程式碼的 `init()` 方法會設定授權。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-155">The `init()` method for the code sets up authorization.</span></span> <span data-ttu-id="7d7fe-156">由於授權是快速入門中所有一切的先決條件，所以讓它成為初始化的一部分很合理。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-156">Since authorization is a precondition for everything in the quickstart, it makes sense to have it as part of initialization.</span></span> 

```go
// Authenticate with the Azure services over OAuth, using a service principal.
func init() {
    oauthConfig, err := adal.NewOAuthConfig(azure.PublicCloud.ActiveDirectoryEndpoint, config.TenantID)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v\n", err)
    }
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        config.ServicePrincipalID,
        config.ServicePrincipalSecret,
        azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("faled to get token: %v\n", err)
    }
}
```

<span data-ttu-id="7d7fe-157">此程式碼可完成兩個授權步驟：</span><span class="sxs-lookup"><span data-stu-id="7d7fe-157">This code completes two steps for authorization:</span></span>

* <span data-ttu-id="7d7fe-158">藉由與 Azure Active Directory 連接來擷取 `TenantID` 的 OAuth 組態資訊。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-158">OAuth configuration information for the `TenantID` is retrieved by interfacing with Azure Active Directory.</span></span> <span data-ttu-id="7d7fe-159">[`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) 物件包含標準 Azure 組態中使用的端點。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-159">The [`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) object contains endpoints used in the standard Azure configuration.</span></span>
* <span data-ttu-id="7d7fe-160">呼叫 [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) 函式。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-160">The [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) function is called.</span></span> <span data-ttu-id="7d7fe-161">此函式會採用 OAuth 資訊與服務主體登入，以及正在使用的 Azure 管理樣式。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-161">This function takes the OAuth information along with the service principal login, as well as which style of Azure management is being used.</span></span> <span data-ttu-id="7d7fe-162">除非您有特定需求並知道您的所作所為，否則此值應該一律為 `.ResourceManagerEndpoint`。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-162">Unless you have specific requirements and know what you're doing, this value should always be `.ResourceManagerEndpoint`.</span></span>

### <a name="flow-of-operations-in-main"></a><span data-ttu-id="7d7fe-163">main() 中的作業流程</span><span class="sxs-lookup"><span data-stu-id="7d7fe-163">Flow of operations in main()</span></span>

<span data-ttu-id="7d7fe-164">`main()` 函式很簡單，只會指出作業流程並執行錯誤檢查。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-164">The `main()` function is simple, only indicating the flow of operations and performing error-checking.</span></span>

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("created group: %v\n", *group.Name)

    log.Println("starting deployment")
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy correctly: %v", err)
    }
    log.Printf("Completed deployment: %v", *result.Name)
    getLogin()
}
```

<span data-ttu-id="7d7fe-165">程式碼依序執行的步驟如下：</span><span class="sxs-lookup"><span data-stu-id="7d7fe-165">The steps that the code runs through are, in order:</span></span>

* <span data-ttu-id="7d7fe-166">建立要部署至的資源群組 (`createGroup()`)</span><span class="sxs-lookup"><span data-stu-id="7d7fe-166">Create the resource group to deploy to (`createGroup()`)</span></span>
* <span data-ttu-id="7d7fe-167">在此群組內建立部署 (`createDeployment()`)</span><span class="sxs-lookup"><span data-stu-id="7d7fe-167">Create the deployment within this group (`createDeployment()`)</span></span>
* <span data-ttu-id="7d7fe-168">取得並顯示所部署 VM 的登入資訊 (`getLogin()`)</span><span class="sxs-lookup"><span data-stu-id="7d7fe-168">Obtain and display login information for the deployed VM (`getLogin()`)</span></span>

### <a name="creating-the-resource-group"></a><span data-ttu-id="7d7fe-169">建立資源群組</span><span class="sxs-lookup"><span data-stu-id="7d7fe-169">Creating the resource group</span></span>

<span data-ttu-id="7d7fe-170">`createGroup()` 函式會建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-170">The `createGroup()` function creates the resource group.</span></span> <span data-ttu-id="7d7fe-171">查看呼叫流程和引數，以示範服務互動在 SDK 中的建構方式。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-171">Looking at the call flow and arguments demonstrates the way that service interactions are structured in the SDK.</span></span>

```go
func createGroup() (group resources.Group, err error) {
        groupsClient := resources.NewGroupsClient(config.SubscriptionID)
        groupsClient.Authorizer = autorest.NewBearerAuthorizer(token)

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

<span data-ttu-id="7d7fe-172">與 Azure 服務互動的一般流程如下：</span><span class="sxs-lookup"><span data-stu-id="7d7fe-172">The general flow of interacting with an Azure service is:</span></span>

* <span data-ttu-id="7d7fe-173">使用 `service.NewXClient()` 方法建立用戶端，其中 `X` 是您要進行互動之 `service` 的資源類型。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-173">Create the client using the `service.NewXClient()` method, where `X` is the resource type of the `service` that you want to interact with.</span></span> <span data-ttu-id="7d7fe-174">此函式一律採用訂用帳戶識別碼。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-174">This function always takes a subscription ID.</span></span>
* <span data-ttu-id="7d7fe-175">設定用戶端的授權方法，允許它與遠端 API 互動。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-175">Set the authorization method for the client, allowing it to interact with the remote API.</span></span>
* <span data-ttu-id="7d7fe-176">請在遠端 API 的對應用戶端上呼叫此方法。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-176">Make the method call on the client corresponding to the remote API.</span></span> <span data-ttu-id="7d7fe-177">服務用戶端方法通常會採用資源的名稱和中繼資料物件。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-177">Service client methods usually take the name of the resource and a metadata object.</span></span>

<span data-ttu-id="7d7fe-178">[`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函式在此用於執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-178">The [`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) function is used to perform a type conversion here.</span></span> <span data-ttu-id="7d7fe-179">SDK 方法的 parameters 結構幾乎會以獨佔方式採用指標，所以提供這些方法可讓您輕鬆地轉換類型。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-179">The parameters structs for methods of the SDK almost exclusively take pointers, so these methods are provided to make the type conversions easy.</span></span> <span data-ttu-id="7d7fe-180">如需便利轉換器的完整清單和行為，請參閱 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 模組的文件。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-180">See the documentation for the [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) module for the complete list and behavior of convenience converters.</span></span>

<span data-ttu-id="7d7fe-181">`groupsClient.CreateOrUpdate()` 作業會傳回資料結構的指標，代表資源群組。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-181">The `groupsClient.CreateOrUpdate()` operation returns a pointer to a data struct representing the resource group.</span></span> <span data-ttu-id="7d7fe-182">這種直接傳回值表示要同步的長時間執行作業。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-182">A direct return value of this kind indicates a short-running operation that is meant to be synchronous.</span></span> <span data-ttu-id="7d7fe-183">在下一節中，您會看到長時間執行的作業範例，以及其互動方式。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-183">In the next section, you'll see an example of a long-running operation and how to interact with them.</span></span>

### <a name="performing-the-deployment"></a><span data-ttu-id="7d7fe-184">執行部署</span><span class="sxs-lookup"><span data-stu-id="7d7fe-184">Performing the deployment</span></span>

<span data-ttu-id="7d7fe-185">一旦建立要容納資源的群組，即可開始執行部署。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-185">Once the group to contain its resources is created, it's time to run the deployment.</span></span> <span data-ttu-id="7d7fe-186">此程式碼會分解成較小的區段，以強調其邏輯的不同部分。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-186">This code is broken up into smaller sections to emphasize different parts of its logic.</span></span>


```go
func createDeployment() (deployment resources.DeploymentExtended, err error) {
    template, err := readJSON(templateFile)
    if err != nil {
        return
    }
    params, err := readJSON(parametersFile)
    if err != nil {
        return
    }

        // ...
```

<span data-ttu-id="7d7fe-187">部署檔案是由 `readJSON` 載入，這裡略過其詳細資料。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-187">The deployment files are loaded by `readJSON`, the details of which are skipped here.</span></span> <span data-ttu-id="7d7fe-188">此函式會傳回 `*map[string]interface{}`，這是用於為資源部署呼叫建構中繼資料的類型。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-188">This function returns a `*map[string]interface{}`, the type used in constructing the metadata for the resource deployment call.</span></span>

```go
        // ...
        
        deploymentsClient := resources.NewDeploymentsClient(config.SubscriptionID)
        deploymentsClient.Authorizer = autorest.NewBearerAuthorizer(token)

        deploymentFuture, err := deploymentsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                deploymentName,
                resources.Deployment{
                        Properties: &resources.DeploymentProperties{
                                Template:   template,
                                Parameters: params,
                                Mode:       resources.Incremental,
                        },
                },
        )
        if err != nil {
                log.Fatalf("Failed to create deployment: %v", err)
        }
        //...
```

<span data-ttu-id="7d7fe-189">此程式碼會遵循建立資源群組時的相同模式。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-189">This code follows the same pattern as with creating the resource group.</span></span> <span data-ttu-id="7d7fe-190">建立新用戶端、獲得向 Azure 驗證的能力，接著呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-190">A new client is created, given the ability to authenticate with Azure, and then a method is called.</span></span> <span data-ttu-id="7d7fe-191">此方法的名稱 (`CreateOrUpdate`) 甚至與資源群組的對應方法名稱相同。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-191">The method even has the same name (`CreateOrUpdate`) as the corresponding method for resource groups.</span></span> <span data-ttu-id="7d7fe-192">此模式會一再出現於 SDK 中。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-192">This pattern is seen again and again in the SDK.</span></span> <span data-ttu-id="7d7fe-193">執行類似工作的方法通常具有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-193">Methods that perform similar work normally have the same name.</span></span>

<span data-ttu-id="7d7fe-194">最大差異在於 `deploymentsClient.CreateOrUpdate()` 方法的傳回值。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-194">The biggest difference comes in the return value of the `deploymentsClient.CreateOrUpdate()` method.</span></span> <span data-ttu-id="7d7fe-195">這個值是 `Future` 物件，它會遵循 [Future 設計模式](https://en.wikipedia.org/wiki/Futures_and_promises)。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-195">This value is a `Future` object, which follows the [future design pattern](https://en.wikipedia.org/wiki/Futures_and_promises).</span></span> <span data-ttu-id="7d7fe-196">Future 代表 Azure 中長時間執行的作業，您可以在執行其他工作時偶爾輪詢該作業。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-196">Futures represent a long-running operation in Azure that you may want to occasionally poll while performing other work.</span></span>

```go
        //...
        err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
        if err != nil {
                log.Fatalf("Error while waiting for deployment creation: %v", err)
        }
        return deploymentFuture.Result(deploymentsClient)
}
```

<span data-ttu-id="7d7fe-197">在此案例中，最佳做法是等候作業完成。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-197">For this example, the best thing to do is to wait for the operation to complete.</span></span> <span data-ttu-id="7d7fe-198">等候 Future 同時需要[內容物件](https://blog.golang.org/context)和建立 Future 物件的用戶端。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-198">Waiting on a future requires both a [context object](https://blog.golang.org/context) and the client that created the Future object.</span></span> <span data-ttu-id="7d7fe-199">有以下兩個可能的錯誤來源：嘗試叫用方法時在用戶端上造成的錯誤，以及來自伺服器的錯誤回應。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-199">There are two possible error sources here: An error caused on the client side when trying to invoke the method, and an error response from the server.</span></span> <span data-ttu-id="7d7fe-200">後者會在 `deploymentFuture.Result()` 呼叫中傳回。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-200">The latter is returned as part of the `deploymentFuture.Result()` call.</span></span>

### <a name="obtaining-the-assigned-ip-address"></a><span data-ttu-id="7d7fe-201">取得指派的 IP 位址</span><span class="sxs-lookup"><span data-stu-id="7d7fe-201">Obtaining the assigned IP address</span></span>

<span data-ttu-id="7d7fe-202">若要使用新建立的 VM 執行任何作業，您需要指派的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-202">To do anything with the newly created VM, you need the assigned IP address.</span></span> <span data-ttu-id="7d7fe-203">IP 位址是自有的獨立 Azure 資源，可繫結至網路介面控制器 (NIC) 資源。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-203">IP addresses are their own separate Azure resource, bound to Network Interface Controller (NIC) resources.</span></span>

```go
func getLogin() {
        params, err := readJSON(parametersFile)
        if err != nil {
                log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
        }

        addressClient := network.NewPublicIPAddressesClient(config.SubscriptionID)
        addressClient.Authorizer = autorest.NewBearerAuthorizer(token)
        ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
        ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
        if err != nil {
                log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
        }

        vmUser := (*params)["vm_user"].(map[string]interface{})
        vmPass := (*params)["vm_password"].(map[string]interface{})

        log.Printf("Log in with ssh: %s@%s, password: %s",
                vmUser["value"].(string),
                *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
                vmPass["value"].(string))
}
```

<span data-ttu-id="7d7fe-204">此方法需依賴儲存在 parameters 檔案中的資訊。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-204">This method relies on information that is stored in the parameters file.</span></span> <span data-ttu-id="7d7fe-205">程式碼可以直接查詢 VM 來取得其 NIC，查詢 NIC 來取得其 IP 資源，然後再直接查詢 IP 資源。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-205">The code could query the VM directly to get its NIC, query the NIC to get its IP resource, and then query the IP resource directly.</span></span> <span data-ttu-id="7d7fe-206">這是一長串要解析的相依性和作業，很耗費資源。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-206">That's a long chain of dependencies and operations to resolve, making it expensive.</span></span> <span data-ttu-id="7d7fe-207">因為 JSON 資訊位於本機，所以可改為載入該資訊。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-207">Since the JSON information is local, it can be loaded instead.</span></span>

<span data-ttu-id="7d7fe-208">VM 使用者和密碼的值同樣會從 JSON 進行載入。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-208">The values for the VM user and password are likewise loaded from the JSON.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7d7fe-209">後續步驟</span><span class="sxs-lookup"><span data-stu-id="7d7fe-209">Next steps</span></span>

<span data-ttu-id="7d7fe-210">在本快速入門中，您採用了現有範本並透過 Go 進行部署。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-210">In this quickstart, you took an existing template and deployed it through Go.</span></span> <span data-ttu-id="7d7fe-211">然後透過 SSH 連線到新建立的 VM，以確保它正在執行中。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-211">Then you connected to the newly created VM via SSH to ensure that it's running.</span></span>

<span data-ttu-id="7d7fe-212">若要繼續了解如何透過 Go 在 Azure 環境中使用虛擬機器，請參閱[適用於 Go 的 Azure 計算範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)或[適用於 Go 的 Azure 資源管理範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。</span><span class="sxs-lookup"><span data-stu-id="7d7fe-212">To continue learning about working with virtual machines in the Azure environment with Go, take a look at the [Azure compute samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) or [Azure resource management samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span></span>
