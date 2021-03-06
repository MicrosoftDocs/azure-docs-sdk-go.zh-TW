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
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>快速入門：使用 Azure SDK for Go 從範本部署 Azure 虛擬機器

本快速入門會示範如何使用 Azure SDK for Go 從 Azure Resource Manager 範本部署資源。 範本是 [Azure 資源群組](/azure/azure-resource-manager/resource-group-overview)中所有資源的快照集。 在這過程中，您將熟悉 SDK 的功能和慣例。

在本快速入門結束時，您會有使用使用者名稱和密碼登入的執行中 VM。

> [!NOTE]
> 若要了解在不使用 Resource Manager 範本的情況下，如何在 Go 中建立 VM，請見此[命令式範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go)，其中會示範如何使用 SDK 來建置和設定所有 VM 資源。 使用此範例中的範本可讓您專注在 SDK 慣例上，無須深入探討太多有關 Azure 服務架構的詳細資料。

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

如果您採用本機安裝 Azure CLI，本快速入門會要求您執行 CLI __2.0.28__ 版或更新版本。 執行 `az --version` 以確定您的 CLI 安裝符合此需求。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="install-the-azure-sdk-for-go"></a>安裝 Azure SDK for Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>建立服務主體

若要使用應用程式透過與 Azure 非互動方式登入，您需要服務主體。 服務主體是角色型存取控制 (RBAC) 的一部分，可建立唯一的使用者身分識別。 若要使用 CLI 建立新的服務主體，請執行下列命令：

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth > quickstart.auth
```

將環境變數 `AZURE_AUTH_LOCATION` 設定為此檔案的完整路徑。 然後 SDK 會從這個檔案中找出並直接讀取認證，您不必進行任何變更或記錄來自服務主體的資訊。

## <a name="get-the-code"></a>取得程式碼

使用 `go get` 來取得快速入門程式碼及其所有相依性。

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

若正確設定了 `AZURE_AUTH_LOCATION` 變數，您就不需要進行任何原始程式碼變更。 程式在執行時，會從該檔案載入所有必要的驗證資訊。

## <a name="running-the-code"></a>執行程式碼

使用 `go run` 命令執行本快速入門。

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

如果部署成功，您會看到一則訊息，提供使用者名稱、IP 位址和密碼，以便登入新建立的虛擬機器。 透過 SSH 連到此機器，以查看它是否已啟動並執行中。 

## <a name="cleaning-up"></a>清除

透過 CLI 刪除資源群組，以清除您在本快速入門期間建立的資源。

```azurecli-interactive
az group delete -n GoVMQuickstart
```

也請刪除已建立的服務主體。 `quickstart.auth` 檔案中有 `clientId` 的 JSON 索引鍵。 將此值複製到 `CLIENT_ID_VALUE` 環境變數，然後執行下列 Azure CLI 命令：

```azurecli-interactive
az ad sp delete --id ${CLIENT_ID_VALUE}
```

其中 `CLIENT_ID_VALUE` 的值來自 `quickstart.auth`。

> [!WARNING]
> 此應用程式的服務主體若未刪除，則會成為您 Azure Active Directory 租用戶中的作用中服務主體。
> 當該服務主體的名稱和密碼產生為 UUID 時，請務必刪除任何未使用的服務主體與 Azure Active Directory 應用程式，以遵循正確的安全性作法。

## <a name="code-in-depth"></a>深入了解程式碼

快速入門程式碼的功用可細分為一組變數和數個小型函式，我們會在此進行討論。

### <a name="variables-constants-and-types"></a>變數、常數和類型

因為快速入門是獨立的，所以會使用全域常數和變數。

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

系統會宣告值，以提供所建立資源的名稱。 在此也會指定位置，您可以變更位置，以查看部署在其他資料中心的行為。 並非每個資料中心都具備所有必要的資源。

`clientInfo` 類型會保留從驗證檔案載入的所有資訊，以便在 SDK 中設定用戶端及設定虛擬機器密碼。

`templateFile` 和 `parametersFile` 常數會指向部署所需的檔案。 Go SDK 會設定 `authorizer` 以進行驗證，而 `ctx` 變數是網路作業用的 [Go 內容](https://blog.golang.org/context)。

### <a name="authentication-and-initialization"></a>驗證與初始化

`init` 函式會設定驗證。 由於驗證是快速入門中所有一切的先決條件，所以讓它成為初始化的一部分很合理。 該函式也會從驗證檔案載入一些資訊，以便設定用戶端和虛擬機器。

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

首先，系統會呼叫 [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile)，從從位 `AZURE_AUTH_LOCATION` 的檔案載入驗證資訊。 接下來，`readJSON` 函式會手動載入此檔案 (在此省略過程) 以提取執行程式其餘部分所需的兩個值：用戶端的訂用帳戶 ID，以及服務主體的祕密 (這也用作虛擬機器密碼)。

> [!WARNING]
> 為了簡化快速入門的步驟，我們會重複使用服務主體密碼。 在生產環境中，__切勿__重複使用能存取您 Azure 資源的密碼。

### <a name="flow-of-operations-in-main"></a>main() 中的作業流程

`main` 函式很簡單，只會指出作業流程並執行錯誤檢查。

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

程式碼依序執行的步驟如下：

* 建立要部署至的資源群組 (`createGroup`)
* 在此群組內建立部署 (`createDeployment`)
* 取得並顯示所部署 VM 的登入資訊 (`getLogin`)

### <a name="create-the-resource-group"></a>建立資源群組

`createGroup` 函式會建立資源群組。 查看呼叫流程和引數，以示範服務互動在 SDK 中的建構方式。

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

與 Azure 服務互動的一般流程如下：

* 使用 `service.New*Client()` 方法建立用戶端，其中 `*` 是您要進行互動之 `service` 的資源類型。 此函式一律採用訂用帳戶識別碼。
* 設定用戶端的授權方法，允許它與遠端 API 互動。
* 請在遠端 API 的對應用戶端上呼叫此方法。 服務用戶端方法通常會採用資源的名稱和中繼資料物件。

[`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函式在此用於執行類型轉換。 SDK 方法的變數結構幾乎全都會採用指標，所以我們提供便利的方法，讓您可輕鬆地轉換類型。 如需便利轉換器及其行為的完整清單，請參閱 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 模組的文件。

`groupsClient.CreateOrUpdate` 方法會傳回資料類型的指標，代表資源群組。 這種直接傳回值表示要同步的長時間執行作業。 在下一節中，您會看到長時間執行的作業範例，以及與其互動的方式。

### <a name="perform-the-deployment"></a>執行部署

一旦建立資源群組，即可開始執行部署。 此程式碼會分解成較小的區段，以強調其邏輯的不同部分。

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

部署檔案是由 `readJSON` 載入，這裡略過其詳細資料。 此函式會傳回 `*map[string]interface{}`，這是用於為資源部署呼叫建構中繼資料的類型。 虛擬機器密碼也要在部署參數上手動設定。

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

此程式碼會遵循建立資源群組時的相同模式。 建立新用戶端、獲得向 Azure 驗證的能力，接著呼叫方法。
此方法的名稱 (`CreateOrUpdate`) 甚至與資源群組的對應方法名稱相同。 在整個 SDK 中都可看到此模式。
執行類似工作的方法通常具有相同的名稱。

最大差異在於 `deploymentsClient.CreateOrUpdate` 方法的傳回值。 這個值屬於 [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future) 類型，會遵循 [Future 設計模式](https://en.wikipedia.org/wiki/Futures_and_promises)。 Future 代表 Azure 中長時間執行的作業，在其完成時可加以輪詢、取消或封鎖。

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    return deploymentFuture.Result(deploymentsClient)
}
```

在此案例中，最佳做法是等候作業完成。 要等候 Future 同時需要[內容物件](https://blog.golang.org/context)和建立 `Future` 的用戶端。 有以下兩個可能的錯誤來源：嘗試叫用方法時在用戶端上造成的錯誤，以及來自伺服器的錯誤回應。 後者會在 `deploymentFuture.Result` 呼叫中傳回。

### <a name="get-the-assigned-ip-address"></a>取得指派的 IP 位址

若要使用新建立的 VM 執行任何作業，您需要指派的 IP 位址。 IP 位址是自有的獨立 Azure 資源，可繫結至網路介面控制器 (NIC) 資源。

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

此方法需依賴儲存在 parameters 檔案中的資訊。 程式碼可以直接查詢 VM 來取得其 NIC，查詢 NIC 來取得其 IP 資源，然後再直接查詢 IP 資源。 這是一長串要解析的相依性和作業，很耗費資源。 因為 JSON 資訊位於本機，所以可改為載入該資訊。

虛擬機器使用者的值也會從 JSON 載入。 虛擬機器密碼之前已從驗證檔案載入。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您採用了現有範本並透過 Go 進行部署。 然後透過 SSH 連線到新建立的 VM。

若要繼續了解如何透過 Go 在 Azure 環境中使用虛擬機器，請參閱[適用於 Go 的 Azure 計算範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)或[適用於 Go 的 Azure 資源管理範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。

若要深入了解 SDK 中可用的驗證方法，及其所支援的驗證類型，請參閱[使用 Azure SDK for Go 進行驗證](azure-sdk-go-authorization.md)。
