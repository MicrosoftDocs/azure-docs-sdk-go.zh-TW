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
ms.openlocfilehash: e530d944deca40e9e6c29b6c2768e2367822714e
ms.sourcegitcommit: aaa8c37880332625f858a38f5918e6cf581bf48d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2018
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>快速入門：使用 Azure SDK for Go 從範本部署 Azure 虛擬機器

本快速入門著重於使用 Azure SDK for Go 從範本部署資源。 範本是 [Azure 資源群組](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)內含之所有資源的快照集。 在執行有用工作的過程中，您將熟悉 SDK 的功能和慣例。

在本快速入門結束時，您會有使用使用者名稱和密碼登入的執行中 VM。

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

如果您採用本機安裝 Azure CLI，本快速入門會要求您執行 CLI 2.0.24 版或更新版本。 執行 `az --version` 以確定您的 CLI 安裝符合此需求。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="install-the-azure-sdk-for-go"></a>安裝 Azure SDK for Go 

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>建立服務主體

若要使用應用程式以非互動方式登入，您需要服務主體。 服務主體是以角色為基礎的驗證 (RBAC) 的一部分，此種驗證可建立唯一的使用者身分識別。 若要使用 CLI 建立新的服務主體，請執行下列命令：

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart
```

__務必__在輸出中記錄 `appId``password` 和 `tenant` 值。 應用程式會使用這些值來向 Azure 進行驗證。

如需使用 Azure CLI 2.0 建立和管理服務主體的詳細資訊，請參閱[使用 Azure CLI 2.0 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

## <a name="get-the-code"></a>取得程式碼

使用 `go get` 來取得快速入門程式碼及其所有相依性。

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm/...
```

此程式碼會編譯，但是要等到您提供您的 Azure 帳戶和所建立服務主體的相關資訊，才會正確執行。 `main.go` 中有 `config` 變數，其中包含 `authInfo` 結構。 此結構的欄位值必須加以取代，才能正確地驗證。

```go
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }
```

* `SubscriptionID`：您的訂用帳戶識別碼，您可以使用下列 CLI 命令取得

  ```azurecli-interactive
  az account show --query id -o tsv
  ```

* `TenantID`：您的租用戶識別碼，建立服務主體時所記錄的 `tenant` 值
* `ServicePrincipalID`：建立服務主體時所記錄的 `appId` 值
* `ServicePrincipalSecret`：建立服務主體時所記錄的 `password` 值

您也必須編輯 `vm-quickstart-params.json` 檔案中的值。

```json
    "vm_password": {
        "value": "_"
    }
```

* `vm_password`：VM 使用者帳戶的密碼。 它的長度必須為 6-72 個字元，而且包含下列 3 個字元：
  * 小寫字母
  * 大寫字母
  * 數字
  * 符號

## <a name="running-the-code"></a>執行程式碼

使用 `go run` 命令執行本快速入門。

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm
go run main.go
```

如果部署失敗，您會收到一則訊息，指出發生問題，但沒有任何特定詳細資料。 透過 Azure CLI，使用下列命令取得部署失敗的詳細資訊：

```azurecli-interactive
az group deployment show -g GoVMQuickstart -n VMDeployQuickstart
```

如果部署成功，您會看到一則訊息，提供使用者名稱、IP 位址和密碼，以便登入新建立的虛擬機器。 透過 SSH 連到這部電腦，以確認它已啟動並執行中。

## <a name="cleaning-up"></a>清除

透過 CLI 刪除資源群組，以清除您在本快速入門期間建立的資源。

```azurecli-interactive
az group delete -n GoVMQuickstart
```

## <a name="code-in-depth"></a>深入了解程式碼

快速入門程式碼的功用可細分為一組變數和數個小型函式，我們會在此進行討論。

### <a name="variable-assignments-and-structs"></a>指派變數與結構

由於快速入門具有獨立性，所以使用全域變數，而不使用命令列選項或環境變數。

```go
type authInfo struct {
        TenantID               string
        SubscriptionID         string
        ServicePrincipalID     string
        ServicePrincipalSecret string
}
```

系統會宣告 `authInfo` 結構，以封裝使用 Azure 服務進行授權所需的全部資訊。

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

系統會宣告值，以提供所建立資源的名稱。 在此也會指定位置，您可以變更位置，以查看部署在其他資料中心的行為。 並非每個資料中心都具備所有必要的資源。

`templateFile` 和 `parametersFile` 常數會指向部署所需的檔案。 服務主體權杖會在稍後說明，而 `ctx` 變數是網路作業的 [Go 內容](https://blog.golang.org/context)。

### <a name="init-and-authorization"></a>init() 和授權

程式碼的 `init()` 方法會設定授權。 由於授權是快速入門中所有一切的先決條件，所以讓它成為初始化的一部分很合理。 

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

此程式碼可完成兩個授權步驟：

* 藉由與 Azure Active Directory 連接來擷取 `TenantID` 的 OAuth 組態資訊。 [`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) 物件包含標準 Azure 組態中使用的端點。
* 呼叫 [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) 函式。 此函式會採用 OAuth 資訊與服務主體登入，以及正在使用的 Azure 管理樣式。 除非您有特定需求並知道您的所作所為，否則此值應該一律為 `.ResourceManagerEndpoint`。

### <a name="flow-of-operations-in-main"></a>main() 中的作業流程

`main()` 函式很簡單，只會指出作業流程並執行錯誤檢查。

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

程式碼依序執行的步驟如下：

* 建立要部署至的資源群組 (`createGroup()`)
* 在此群組內建立部署 (`createDeployment()`)
* 取得並顯示所部署 VM 的登入資訊 (`getLogin()`)

### <a name="creating-the-resource-group"></a>建立資源群組

`createGroup()` 函式會建立資源群組。 查看呼叫流程和引數，以示範服務互動在 SDK 中的建構方式。

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

與 Azure 服務互動的一般流程如下：

* 使用 `service.NewXClient()` 方法建立用戶端，其中 `X` 是您要進行互動之 `service` 的資源類型。 此函式一律採用訂用帳戶識別碼。
* 設定用戶端的授權方法，允許它與遠端 API 互動。
* 請在遠端 API 的對應用戶端上呼叫此方法。 服務用戶端方法通常會採用資源的名稱和中繼資料物件。

[`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) 函式在此用於執行類型轉換。 SDK 方法的 parameters 結構幾乎會以獨佔方式採用指標，所以提供這些方法可讓您輕鬆地轉換類型。 如需便利轉換器的完整清單和行為，請參閱 [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) 模組的文件。

`groupsClient.CreateOrUpdate()` 作業會傳回資料結構的指標，代表資源群組。 這種直接傳回值表示要同步的長時間執行作業。 在下一節中，您會看到長時間執行的作業範例，以及其互動方式。

### <a name="performing-the-deployment"></a>執行部署

一旦建立要容納資源的群組，即可開始執行部署。 此程式碼會分解成較小的區段，以強調其邏輯的不同部分。


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

部署檔案是由 `readJSON` 載入，這裡略過其詳細資料。 此函式會傳回 `*map[string]interface{}`，這是用於為資源部署呼叫建構中繼資料的類型。

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

此程式碼會遵循建立資源群組時的相同模式。 建立新用戶端、獲得向 Azure 驗證的能力，接著呼叫方法。 此方法的名稱 (`CreateOrUpdate`) 甚至與資源群組的對應方法名稱相同。 此模式會一再出現於 SDK 中。 執行類似工作的方法通常具有相同的名稱。

最大差異在於 `deploymentsClient.CreateOrUpdate()` 方法的傳回值。 這個值是 `Future` 物件，它會遵循 [Future 設計模式](https://en.wikipedia.org/wiki/Futures_and_promises)。 Future 代表 Azure 中長時間執行的作業，您可以在執行其他工作時偶爾輪詢該作業。

```go
        //...
        err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
        if err != nil {
                log.Fatalf("Error while waiting for deployment creation: %v", err)
        }
        return deploymentFuture.Result(deploymentsClient)
}
```

在此案例中，最佳做法是等候作業完成。 等候 Future 同時需要[內容物件](https://blog.golang.org/context)和建立 Future 物件的用戶端。 有以下兩個可能的錯誤來源：嘗試叫用方法時在用戶端上造成的錯誤，以及來自伺服器的錯誤回應。 後者會在 `deploymentFuture.Result()` 呼叫中傳回。

### <a name="obtaining-the-assigned-ip-address"></a>取得指派的 IP 位址

若要使用新建立的 VM 執行任何作業，您需要指派的 IP 位址。 IP 位址是自有的獨立 Azure 資源，可繫結至網路介面控制器 (NIC) 資源。

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

此方法需依賴儲存在 parameters 檔案中的資訊。 程式碼可以直接查詢 VM 來取得其 NIC，查詢 NIC 來取得其 IP 資源，然後再直接查詢 IP 資源。 這是一長串要解析的相依性和作業，很耗費資源。 因為 JSON 資訊位於本機，所以可改為載入該資訊。

VM 使用者和密碼的值同樣會從 JSON 進行載入。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您採用了現有範本並透過 Go 進行部署。 然後透過 SSH 連線到新建立的 VM，以確保它正在執行中。

若要繼續了解如何透過 Go 在 Azure 環境中使用虛擬機器，請參閱[適用於 Go 的 Azure 計算範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)或[適用於 Go 的 Azure 資源管理範例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources)。
