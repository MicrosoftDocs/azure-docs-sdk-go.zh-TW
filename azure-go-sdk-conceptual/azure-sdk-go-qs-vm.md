---
title: 從 Go 部署 Azure 虛擬機器
description: 使用 Azure SDK for Go 部署 Azure 虛擬機器。
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: quickstart
ms.technology: azure-sdk-go
ms.service: virtual-machines
ms.devlang: go
ms.openlocfilehash: a7970be0857fd414d776241b033af0c23457790c
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059130"
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a><span data-ttu-id="915f9-103">快速入門：使用 Azure SDK for Go 從範本部署 Azure 虛擬機器</span><span class="sxs-lookup"><span data-stu-id="915f9-103">Quickstart: Deploy an Azure virtual machine from a template with the Azure SDK for Go</span></span>

<span data-ttu-id="915f9-104">本快速入門會示範如何使用 Azure SDK for Go 從 Azure Resource Manager 範本部署資源。</span><span class="sxs-lookup"><span data-stu-id="915f9-104">This quickstart shows you how to deploy resources from an Azure Resource Manager template, using the Azure SDK for Go.</span></span> <span data-ttu-id="915f9-105">範本是 [Azure 資源群組](/azure/azure-resource-manager/resource-group-overview)中所有資源的快照集。</span><span class="sxs-lookup"><span data-stu-id="915f9-105">Templates are snapshots of all of the resources within an [Azure resource group](/azure/azure-resource-manager/resource-group-overview).</span></span> <span data-ttu-id="915f9-106">在這過程中，您將熟悉 SDK 的功能和慣例。</span><span class="sxs-lookup"><span data-stu-id="915f9-106">Along the way, you'll become familiar with the functionality and conventions of the SDK.</span></span>

<span data-ttu-id="915f9-107">在本快速入門結束時，您會有使用使用者名稱和密碼登入的執行中 VM。</span><span class="sxs-lookup"><span data-stu-id="915f9-107">At the end of this quickstart, you have a running VM that you log into with a username and password.</span></span>

> [!NOTE]
> <span data-ttu-id="915f9-108">若要了解在不使用 Resource Manager 範本的情況下，如何在 Go 中建立 VM，請見此[命令式範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go)，其中會示範如何使用 SDK 來建置和設定所有 VM 資源。</span><span class="sxs-lookup"><span data-stu-id="915f9-108">To see the creation of a VM in Go without the use of a Resource Manager template, there is an [imperative sample](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go) demonstrating how to build and configure all VM resources with the SDK.</span></span> <span data-ttu-id="915f9-109">使用此範例中的範本可讓您專注在 SDK 慣例上，無須深入探討太多有關 Azure 服務架構的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="915f9-109">Using a template in this sample allows a focus on SDK conventions without getting into too many details about Azure service architecture.</span></span>

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

<span data-ttu-id="915f9-110">如果您採用本機安裝 Azure CLI，本快速入門會要求您執行 CLI __2.0.28__ 版或更新版本。</span><span class="sxs-lookup"><span data-stu-id="915f9-110">If you use a local install of the Azure CLI, this quickstart requires CLI version __2.0.28__ or later.</span></span> <span data-ttu-id="915f9-111">執行 `az --version` 以確定您的 CLI 安裝符合此需求。</span><span class="sxs-lookup"><span data-stu-id="915f9-111">Run `az --version` to make sure your CLI install meets this requirement.</span></span> <span data-ttu-id="915f9-112">如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。</span><span class="sxs-lookup"><span data-stu-id="915f9-112">If you need to install or upgrade, see [Install the Azure CLI](/cli/azure/install-azure-cli).</span></span>

## <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="915f9-113">安裝 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="915f9-113">Install the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a><span data-ttu-id="915f9-114">建立服務主體</span><span class="sxs-lookup"><span data-stu-id="915f9-114">Create a service principal</span></span>

<span data-ttu-id="915f9-115">若要使用應用程式透過與 Azure 非互動方式登入，您需要服務主體。</span><span class="sxs-lookup"><span data-stu-id="915f9-115">To sign in non-interactively to Azure with an application, you need a service principal.</span></span> <span data-ttu-id="915f9-116">服務主體是角色型存取控制 (RBAC) 的一部分，可建立唯一的使用者身分識別。</span><span class="sxs-lookup"><span data-stu-id="915f9-116">Service principals are part of role-based access control (RBAC), which creates a unique user identity.</span></span> <span data-ttu-id="915f9-117">若要使用 CLI 建立新的服務主體，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="915f9-117">To create a new service principal with the CLI, run the following command:</span></span>

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth > quickstart.auth
```

<span data-ttu-id="915f9-118">將環境變數 `AZURE_AUTH_LOCATION` 設定為此檔案的完整路徑。</span><span class="sxs-lookup"><span data-stu-id="915f9-118">Set the environment variable `AZURE_AUTH_LOCATION` to be the full path to this file.</span></span> <span data-ttu-id="915f9-119">然後 SDK 會從這個檔案中找出並直接讀取認證，您不必進行任何變更或記錄來自服務主體的資訊。</span><span class="sxs-lookup"><span data-stu-id="915f9-119">Then the SDK locates and reads the credentials directly from this file, without you having to make any changes or record information from the service principal.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="915f9-120">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="915f9-120">Get the code</span></span>

<span data-ttu-id="915f9-121">使用 `go get` 來取得快速入門程式碼及其所有相依性。</span><span class="sxs-lookup"><span data-stu-id="915f9-121">Get the quickstart code and all of its dependencies with `go get`.</span></span>

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

<span data-ttu-id="915f9-122">若正確設定了 `AZURE_AUTH_LOCATION` 變數，您就不需要進行任何原始程式碼變更。</span><span class="sxs-lookup"><span data-stu-id="915f9-122">You don't need to make any source code modifications if the `AZURE_AUTH_LOCATION` variable is properly set.</span></span> <span data-ttu-id="915f9-123">程式在執行時，會從該檔案載入所有必要的驗證資訊。</span><span class="sxs-lookup"><span data-stu-id="915f9-123">When the program runs, it loads all the necessary authentication information from there.</span></span>

## <a name="running-the-code"></a><span data-ttu-id="915f9-124">執行程式碼</span><span class="sxs-lookup"><span data-stu-id="915f9-124">Running the code</span></span>

<span data-ttu-id="915f9-125">使用 `go run` 命令執行本快速入門。</span><span class="sxs-lookup"><span data-stu-id="915f9-125">Run the quickstart with the `go run` command.</span></span>

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

<span data-ttu-id="915f9-126">如果部署成功，您會看到一則訊息，提供使用者名稱、IP 位址和密碼，以便登入新建立的虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="915f9-126">If the deployment is successful, you see a message giving the username, IP address, and password for logging into the newly created virtual machine.</span></span> <span data-ttu-id="915f9-127">透過 SSH 連到此機器，以查看它是否已啟動並執行中。</span><span class="sxs-lookup"><span data-stu-id="915f9-127">SSH into this machine to see if it's up and running.</span></span> 

## <a name="cleaning-up"></a><span data-ttu-id="915f9-128">清除</span><span class="sxs-lookup"><span data-stu-id="915f9-128">Cleaning up</span></span>

<span data-ttu-id="915f9-129">透過 CLI 刪除資源群組，以清除您在本快速入門期間建立的資源。</span><span class="sxs-lookup"><span data-stu-id="915f9-129">Clean up the resources created during this quickstart by deleting the resource group with the CLI.</span></span>

```azurecli-interactive
az group delete -n GoVMQuickstart
```

<span data-ttu-id="915f9-130">也請刪除已建立的服務主體。</span><span class="sxs-lookup"><span data-stu-id="915f9-130">Also delete the service principal that was created.</span></span> <span data-ttu-id="915f9-131">`quickstart.auth` 檔案中有 `clientId` 的 JSON 索引鍵。</span><span class="sxs-lookup"><span data-stu-id="915f9-131">In the `quickstart.auth` file, there's a JSON key for `clientId`.</span></span> <span data-ttu-id="915f9-132">將此值複製到 `CLIENT_ID_VALUE` 環境變數，然後執行下列 Azure CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="915f9-132">Copy this value to the `CLIENT_ID_VALUE` environment variable and run the following Azure CLI command:</span></span>

```azurecli-interactive
az ad sp delete --id ${CLIENT_ID_VALUE}
```

<span data-ttu-id="915f9-133">其中 `CLIENT_ID_VALUE` 的值來自 `quickstart.auth`。</span><span class="sxs-lookup"><span data-stu-id="915f9-133">Where you supply the value for `CLIENT_ID_VALUE` from `quickstart.auth`.</span></span>

> [!WARNING]
> <span data-ttu-id="915f9-134">此應用程式的服務主體若未刪除，則會成為您 Azure Active Directory 租用戶中的作用中服務主體。</span><span class="sxs-lookup"><span data-stu-id="915f9-134">Failing to delete the service principal for this application leaves it active in your Azure Active Directory tenant.</span></span>
> <span data-ttu-id="915f9-135">當該服務主體的名稱和密碼產生為 UUID 時，請務必刪除任何未使用的服務主體與 Azure Active Directory 應用程式，以遵循正確的安全性作法。</span><span class="sxs-lookup"><span data-stu-id="915f9-135">While both the name and password for the service principal are generated as UUIDs, make sure that you follow good security practices by deleting any unused service principals and Azure Active Directory Applications.</span></span>

## <a name="code-in-depth"></a><span data-ttu-id="915f9-136">深入了解程式碼</span><span class="sxs-lookup"><span data-stu-id="915f9-136">Code in depth</span></span>

<span data-ttu-id="915f9-137">快速入門程式碼的功用可細分為一組變數和數個小型函式，我們會在此進行討論。</span><span class="sxs-lookup"><span data-stu-id="915f9-137">What the quickstart code does is broken down into a block of variables and several small functions, each of which are discussed here.</span></span>

### <a name="variables-constants-and-types"></a><span data-ttu-id="915f9-138">變數、常數和類型</span><span class="sxs-lookup"><span data-stu-id="915f9-138">Variables, constants, and types</span></span>

<span data-ttu-id="915f9-139">因為快速入門是獨立的，所以會使用全域常數和變數。</span><span class="sxs-lookup"><span data-stu-id="915f9-139">Since quickstart is self-contained, it uses global constants and variables.</span></span>

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

// Information loaded from the authorization file to identify the client
type clientInfo struct {
    SubscriptionID string
    VMPassword     string
}

var (
    ctx        = context.Background()
    clientData clientInfo
    authorizer autorest.Authorizer
)
```

<span data-ttu-id="915f9-140">系統會宣告值，以提供所建立資源的名稱。</span><span class="sxs-lookup"><span data-stu-id="915f9-140">Values are declared which give the names of created resources.</span></span> <span data-ttu-id="915f9-141">在此也會指定位置，您可以變更位置，以查看部署在其他資料中心的行為。</span><span class="sxs-lookup"><span data-stu-id="915f9-141">The location is also specified here, which you can change to see how deployments behave in other datacenters.</span></span> <span data-ttu-id="915f9-142">並非每個資料中心都具備所有必要的資源。</span><span class="sxs-lookup"><span data-stu-id="915f9-142">Not every datacenter has all of the required resources available.</span></span>

<span data-ttu-id="915f9-143">`clientInfo` 類型會保留從驗證檔案載入的所有資訊，以便在 SDK 中設定用戶端及設定虛擬機器密碼。</span><span class="sxs-lookup"><span data-stu-id="915f9-143">The `clientInfo` type holds the information loaded from the authentication file to set up clients in the SDK and set the VM password.</span></span>

<span data-ttu-id="915f9-144">`templateFile` 和 `parametersFile` 常數會指向部署所需的檔案。</span><span class="sxs-lookup"><span data-stu-id="915f9-144">The `templateFile` and `parametersFile` constants point to the files needed for deployment.</span></span> <span data-ttu-id="915f9-145">Go SDK 會設定 `authorizer` 以進行驗證，而 `ctx` 變數是網路作業用的 [Go 內容](https://blog.golang.org/context)。</span><span class="sxs-lookup"><span data-stu-id="915f9-145">The `authorizer` will be configured by the Go SDK for authentication, and the `ctx` variable is a [Go context](https://blog.golang.org/context) for the network operations.</span></span>

### <a name="authentication-and-initialization"></a><span data-ttu-id="915f9-146">驗證與初始化</span><span class="sxs-lookup"><span data-stu-id="915f9-146">Authentication and initialization</span></span>

<span data-ttu-id="915f9-147">`init` 函式會設定驗證。</span><span class="sxs-lookup"><span data-stu-id="915f9-147">The `init` function sets up authentication.</span></span> <span data-ttu-id="915f9-148">由於驗證是快速入門中所有一切的先決條件，所以讓它成為初始化的一部分很合理。</span><span class="sxs-lookup"><span data-stu-id="915f9-148">Since authentication is a precondition for everything in the quickstart, it makes sense to have it as part of initialization.</span></span> <span data-ttu-id="915f9-149">該函式也會從驗證檔案載入一些資訊，以便設定用戶端和虛擬機器。</span><span class="sxs-lookup"><span data-stu-id="915f9-149">It also loads some information needed from the authentication file to configure clients and the VM.</span></span>

```go
func init() {
    var err error
    authorizer, err = auth.NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v", err)
    }

    authInfo, err := readJSON(os.Getenv("AZURE_AUTH_LOCATION"))
    clientData.SubscriptionID = (*authInfo)["subscriptionId"].(string)
    clientData.VMPassword = (*authInfo)["clientSecret"].(string)
}
```

<span data-ttu-id="915f9-150">首先，系統會呼叫 [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile)，從從位 `AZURE_AUTH_LOCATION` 的檔案載入驗證資訊。</span><span class="sxs-lookup"><span data-stu-id="915f9-150">First, [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) is called to load the authentication information from the file located at `AZURE_AUTH_LOCATION`.</span></span> <span data-ttu-id="915f9-151">接下來，`readJSON` 函式會手動載入此檔案 (在此省略過程) 以提取執行程式其餘部分所需的兩個值：用戶端的訂用帳戶 ID，以及服務主體的祕密 (這也用作虛擬機器密碼)。</span><span class="sxs-lookup"><span data-stu-id="915f9-151">Next, this file is loaded manually by the `readJSON` function (omitted here) to pull the two values needed to run the rest of the program: The subscription ID of the client, and the service principal's secret, which is also used for the VM's password.</span></span>

> [!WARNING]
> <span data-ttu-id="915f9-152">為了簡化快速入門的步驟，我們會重複使用服務主體密碼。</span><span class="sxs-lookup"><span data-stu-id="915f9-152">To keep the quickstart simple, the service principal password is reused.</span></span> <span data-ttu-id="915f9-153">在生產環境中，__切勿__重複使用能存取您 Azure 資源的密碼。</span><span class="sxs-lookup"><span data-stu-id="915f9-153">In production, take care to __never__ reuse a password which gives access to your Azure resources.</span></span>

### <a name="flow-of-operations-in-main"></a><span data-ttu-id="915f9-154">main() 中的作業流程</span><span class="sxs-lookup"><span data-stu-id="915f9-154">Flow of operations in main()</span></span>

<span data-ttu-id="915f9-155">`main` 函式很簡單，只會指出作業流程並執行錯誤檢查。</span><span class="sxs-lookup"><span data-stu-id="915f9-155">The `main` function is simple, only indicating the flow of operations and performing error-checking.</span></span>

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("Created group: %v", *group.Name)

    log.Printf("Starting deployment: %s", deploymentName)
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy: %v", err)
    }
    if result.Name != nil {
        log.Printf("Completed deployment %v: %v", deploymentName, *result.Properties.ProvisioningState)
    } else {
        log.Printf("Completed deployment %v (no data returned to SDK)", deploymentName)
    }
    getLogin()
}
```

<span data-ttu-id="915f9-156">程式碼依序執行的步驟如下：</span><span class="sxs-lookup"><span data-stu-id="915f9-156">The steps that the code runs through are, in order:</span></span>

* <span data-ttu-id="915f9-157">建立要部署至的資源群組 (`createGroup`)</span><span class="sxs-lookup"><span data-stu-id="915f9-157">Create the resource group to deploy to (`createGroup`)</span></span>
* <span data-ttu-id="915f9-158">在此群組內建立部署 (`createDeployment`)</span><span class="sxs-lookup"><span data-stu-id="915f9-158">Create the deployment within this group (`createDeployment`)</span></span>
* <span data-ttu-id="915f9-159">取得並顯示所部署 VM 的登入資訊 (`getLogin`)</span><span class="sxs-lookup"><span data-stu-id="915f9-159">Get and display login information for the deployed VM (`getLogin`)</span></span>

### <a name="create-the-resource-group"></a><span data-ttu-id="915f9-160">建立資源群組</span><span class="sxs-lookup"><span data-stu-id="915f9-160">Create the resource group</span></span>

<span data-ttu-id="915f9-161">`createGroup` 函式會建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="915f9-161">The `createGroup` function creates the resource group.</span></span> <span data-ttu-id="915f9-162">查看呼叫流程和引數，以示範服務互動在 SDK 中的建構方式。</span><span class="sxs-lookup"><span data-stu-id="915f9-162">Looking at the call flow and arguments demonstrates the way that service interactions are structured in the SDK.</span></span>

```go
func createGroup() (group resources.Group, err error) {
    groupsClient := resources.NewGroupsClient(clientData.SubscriptionID)
    groupsClient.Authorizer = authorizer

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

<span data-ttu-id="915f9-163">與 Azure 服務互動的一般流程如下：</span><span class="sxs-lookup"><span data-stu-id="915f9-163">The general flow of interacting with an Azure service is:</span></span>

* <span data-ttu-id="915f9-164">使用 `service.New*Client()` 方法建立用戶端，其中 `*` 是您要進行互動之 `service` 的資源類型。</span><span class="sxs-lookup"><span data-stu-id="915f9-164">Create the client using the `service.New*Client()` method, where `*` is the resource type of the `service` that you want to interact with.</span></span> <span data-ttu-id="915f9-165">此函式一律採用訂用帳戶識別碼。</span><span class="sxs-lookup"><span data-stu-id="915f9-165">This function always takes a subscription ID.</span></span>
* <span data-ttu-id="915f9-166">設定用戶端的授權方法，允許它與遠端 API 互動。</span><span class="sxs-lookup"><span data-stu-id="915f9-166">Set the authorization method for the client, allowing it to interact with the remote API.</span></span>
* <span data-ttu-id="915f9-167">請在遠端 API 的對應用戶端上呼叫此方法。</span><span class="sxs-lookup"><span data-stu-id="915f9-167">Make the method call on the client corresponding to the remote API.</span></span> <span data-ttu-id="915f9-168">服務用戶端方法通常會採用資源的名稱和中繼資料物件。</span><span class="sxs-lookup"><span data-stu-id="915f9-168">Service client methods usually take the name of the resource and a metadata object.</span></span>

<span data-ttu-id="915f9-169">[`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函式在此用於執行類型轉換。</span><span class="sxs-lookup"><span data-stu-id="915f9-169">The [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) function is used to perform a type conversion here.</span></span> <span data-ttu-id="915f9-170">SDK 方法的變數結構幾乎全都會採用指標，所以我們提供便利的方法，讓您可輕鬆地轉換類型。</span><span class="sxs-lookup"><span data-stu-id="915f9-170">The parameters for SDK methods almost exclusively take pointers, so convenience methods are provided to make the type conversions easy.</span></span> <span data-ttu-id="915f9-171">如需便利轉換器及其行為的完整清單，請參閱 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 模組的文件。</span><span class="sxs-lookup"><span data-stu-id="915f9-171">See the documentation for the [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) module for the complete list of convenience converters and their behavior.</span></span>

<span data-ttu-id="915f9-172">`groupsClient.CreateOrUpdate` 方法會傳回資料類型的指標，代表資源群組。</span><span class="sxs-lookup"><span data-stu-id="915f9-172">The `groupsClient.CreateOrUpdate` method returns a pointer to a data type representing the resource group.</span></span> <span data-ttu-id="915f9-173">這種直接傳回值表示要同步的長時間執行作業。</span><span class="sxs-lookup"><span data-stu-id="915f9-173">A direct return value of this kind indicates a short-running operation that is meant to be synchronous.</span></span> <span data-ttu-id="915f9-174">在下一節中，您會看到長時間執行的作業範例，以及與其互動的方式。</span><span class="sxs-lookup"><span data-stu-id="915f9-174">In the next section, you'll see an example of a long-running operation and how to interact with it.</span></span>

### <a name="perform-the-deployment"></a><span data-ttu-id="915f9-175">執行部署</span><span class="sxs-lookup"><span data-stu-id="915f9-175">Perform the deployment</span></span>

<span data-ttu-id="915f9-176">一旦建立資源群組，即可開始執行部署。</span><span class="sxs-lookup"><span data-stu-id="915f9-176">Once the resource group is created, it's time to run the deployment.</span></span> <span data-ttu-id="915f9-177">此程式碼會分解成較小的區段，以強調其邏輯的不同部分。</span><span class="sxs-lookup"><span data-stu-id="915f9-177">This code is broken up into smaller sections to emphasize different parts of its logic.</span></span>

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
    (*params)["vm_password"] = map[string]string{
        "value": clientData.VMPassword,
    }
        // ...
```

<span data-ttu-id="915f9-178">部署檔案是由 `readJSON` 載入，這裡略過其詳細資料。</span><span class="sxs-lookup"><span data-stu-id="915f9-178">The deployment files are loaded by `readJSON`, the details of which are skipped here.</span></span> <span data-ttu-id="915f9-179">此函式會傳回 `*map[string]interface{}`，這是用於為資源部署呼叫建構中繼資料的類型。</span><span class="sxs-lookup"><span data-stu-id="915f9-179">This function returns a `*map[string]interface{}`, the type used in constructing the metadata for the resource deployment call.</span></span> <span data-ttu-id="915f9-180">虛擬機器密碼也要在部署參數上手動設定。</span><span class="sxs-lookup"><span data-stu-id="915f9-180">The VM's password is also set manually on the deployment parameters.</span></span>

```go
        // ...

    deploymentsClient := resources.NewDeploymentsClient(clientData.SubscriptionID)
    deploymentsClient.Authorizer = authorizer

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
        return
    }
```

<span data-ttu-id="915f9-181">此程式碼會遵循建立資源群組時的相同模式。</span><span class="sxs-lookup"><span data-stu-id="915f9-181">This code follows the same pattern as creating the resource group.</span></span> <span data-ttu-id="915f9-182">建立新用戶端、獲得向 Azure 驗證的能力，接著呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="915f9-182">A new client is created, given the ability to authenticate with Azure, and then a method is called.</span></span>
<span data-ttu-id="915f9-183">此方法的名稱 (`CreateOrUpdate`) 甚至與資源群組的對應方法名稱相同。</span><span class="sxs-lookup"><span data-stu-id="915f9-183">The method even has the same name (`CreateOrUpdate`) as the corresponding method for resource groups.</span></span> <span data-ttu-id="915f9-184">在整個 SDK 中都可看到此模式。</span><span class="sxs-lookup"><span data-stu-id="915f9-184">This pattern is seen throughout the SDK.</span></span>
<span data-ttu-id="915f9-185">執行類似工作的方法通常具有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="915f9-185">Methods that perform similar work normally have the same name.</span></span>

<span data-ttu-id="915f9-186">最大差異在於 `deploymentsClient.CreateOrUpdate` 方法的傳回值。</span><span class="sxs-lookup"><span data-stu-id="915f9-186">The biggest difference comes in the return value of the `deploymentsClient.CreateOrUpdate` method.</span></span> <span data-ttu-id="915f9-187">這個值屬於 [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future) 類型，會遵循 [Future 設計模式](https://en.wikipedia.org/wiki/Futures_and_promises)。</span><span class="sxs-lookup"><span data-stu-id="915f9-187">This value is of the [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future) type, which follows the [future design pattern](https://en.wikipedia.org/wiki/Futures_and_promises).</span></span> <span data-ttu-id="915f9-188">Future 代表 Azure 中長時間執行的作業，在其完成時可加以輪詢、取消或封鎖。</span><span class="sxs-lookup"><span data-stu-id="915f9-188">Futures represent a long-running operation in Azure that you can poll, cancel, or block on their completion.</span></span>

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    return deploymentFuture.Result(deploymentsClient)
}
```

<span data-ttu-id="915f9-189">在此案例中，最佳做法是等候作業完成。</span><span class="sxs-lookup"><span data-stu-id="915f9-189">For this example, the best thing to do is to wait for the operation to complete.</span></span> <span data-ttu-id="915f9-190">要等候 Future 同時需要[內容物件](https://blog.golang.org/context)和建立 `Future` 的用戶端。</span><span class="sxs-lookup"><span data-stu-id="915f9-190">Waiting on a future requires both a [context object](https://blog.golang.org/context) and the client that created the `Future`.</span></span> <span data-ttu-id="915f9-191">有以下兩個可能的錯誤來源：嘗試叫用方法時在用戶端上造成的錯誤，以及來自伺服器的錯誤回應。</span><span class="sxs-lookup"><span data-stu-id="915f9-191">There are two possible error sources here: An error caused on the client side when trying to invoke the method, and an error response from the server.</span></span> <span data-ttu-id="915f9-192">後者會在 `deploymentFuture.Result` 呼叫中傳回。</span><span class="sxs-lookup"><span data-stu-id="915f9-192">The latter is returned as part of the `deploymentFuture.Result` call.</span></span>

### <a name="get-the-assigned-ip-address"></a><span data-ttu-id="915f9-193">取得指派的 IP 位址</span><span class="sxs-lookup"><span data-stu-id="915f9-193">Get the assigned IP address</span></span>

<span data-ttu-id="915f9-194">若要使用新建立的 VM 執行任何作業，您需要指派的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="915f9-194">To do anything with the newly created VM, you need the assigned IP address.</span></span> <span data-ttu-id="915f9-195">IP 位址是自有的獨立 Azure 資源，可繫結至網路介面控制器 (NIC) 資源。</span><span class="sxs-lookup"><span data-stu-id="915f9-195">IP addresses are their own separate Azure resource, bound to Network Interface Controller (NIC) resources.</span></span>

```go
func getLogin() {
    params, err := readJSON(parametersFile)
    if err != nil {
        log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
    }

    addressClient := network.NewPublicIPAddressesClient(clientData.SubscriptionID)
    addressClient.Authorizer = authorizer
    ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
    ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
    if err != nil {
        log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
    }

    vmUser := (*params)["vm_user"].(map[string]interface{})

    log.Printf("Log in with ssh: %s@%s, password: %s",
        vmUser["value"].(string),
        *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
        clientData.VMPassword)
}
```

<span data-ttu-id="915f9-196">此方法需依賴儲存在 parameters 檔案中的資訊。</span><span class="sxs-lookup"><span data-stu-id="915f9-196">This method relies on information that is stored in the parameters file.</span></span> <span data-ttu-id="915f9-197">程式碼可以直接查詢 VM 來取得其 NIC，查詢 NIC 來取得其 IP 資源，然後再直接查詢 IP 資源。</span><span class="sxs-lookup"><span data-stu-id="915f9-197">The code could query the VM directly to get its NIC, query the NIC to get its IP resource, and then query the IP resource directly.</span></span> <span data-ttu-id="915f9-198">這是一長串要解析的相依性和作業，很耗費資源。</span><span class="sxs-lookup"><span data-stu-id="915f9-198">That's a long chain of dependencies and operations to resolve, making it expensive.</span></span> <span data-ttu-id="915f9-199">因為 JSON 資訊位於本機，所以可改為載入該資訊。</span><span class="sxs-lookup"><span data-stu-id="915f9-199">Since the JSON information is local, it can be loaded instead.</span></span>

<span data-ttu-id="915f9-200">虛擬機器使用者的值也會從 JSON 載入。</span><span class="sxs-lookup"><span data-stu-id="915f9-200">The value for the VM user is also loaded from the JSON.</span></span> <span data-ttu-id="915f9-201">虛擬機器密碼之前已從驗證檔案載入。</span><span class="sxs-lookup"><span data-stu-id="915f9-201">The VM password was loaded earlier from the authentication file.</span></span>

## <a name="next-steps"></a><span data-ttu-id="915f9-202">後續步驟</span><span class="sxs-lookup"><span data-stu-id="915f9-202">Next steps</span></span>

<span data-ttu-id="915f9-203">在本快速入門中，您採用了現有範本並透過 Go 進行部署。</span><span class="sxs-lookup"><span data-stu-id="915f9-203">In this quickstart, you took an existing template and deployed it through Go.</span></span> <span data-ttu-id="915f9-204">然後透過 SSH 連線到新建立的 VM。</span><span class="sxs-lookup"><span data-stu-id="915f9-204">Then you connected to the newly created VM via SSH.</span></span>

<span data-ttu-id="915f9-205">若要繼續了解如何透過 Go 在 Azure 環境中使用虛擬機器，請參閱[適用於 Go 的 Azure 計算範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)或[適用於 Go 的 Azure 資源管理範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。</span><span class="sxs-lookup"><span data-stu-id="915f9-205">To continue learning about working with virtual machines in the Azure environment with Go, take a look at the [Azure compute samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) or [Azure resource management samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span></span>

<span data-ttu-id="915f9-206">若要深入了解 SDK 中可用的驗證方法，及其所支援的驗證類型，請參閱[使用 Azure SDK for Go 進行驗證](azure-sdk-go-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="915f9-206">To learn more about the available authentication methods in the SDK, and which authentication types they support, see [Authentication with the Azure SDK for Go](azure-sdk-go-authorization.md).</span></span>
