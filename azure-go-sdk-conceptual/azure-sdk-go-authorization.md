---
title: 使用 Azure SDK for Go 進行驗證
description: 了解 Azure SDK for Go 中可用的驗證方法及其使用方式。
services: azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 04/03/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.service: active-directory
ms.component: authentication
ms.openlocfilehash: 370f5607b89c0044022f7987d06c3a55c9d6f352
ms.sourcegitcommit: f08abf902b48f8173aa6e261084ff2cfc9043305
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
ms.locfileid: "32319878"
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a>Azure SDK for Go 中可用的驗證方法

Azure SDK for Go 提供各種不同的驗證類型和方法，供您的應用程式使用。 支援的方法範圍包括從環境變數中提取資訊，到網路式互動式驗證。 本文會向您介紹 SDK 中可用的驗證類型，以及使用驗證類型的方法。 您也會了解在選取最適合應用程式的驗證類型時，怎樣才是最佳作法。

## <a name="available-authentication-types-and-methods"></a>可用的驗證類型和方法

Azure SDK for Go 提供數種不同類型的驗證，各自使用不同的認證集。 每種驗證類型都可以透過不同的驗證方法加以使用；而這些驗證方法就是 SDK 採用認證作為輸入值的方式。 下表描述可用的驗證類型，以及建議您的應用程式使用該驗證類型的情況。

| 驗證類型 | 建議使用的時機... |
|---------------------|---------------------|
| 憑證式驗證 | 您所具有的 X509 憑證，已針對 Azure Active Directory (AAD) 使用者或服務主體進行設定。 若要深入了解，請參閱[開始在 Azure Active Directory 中使用憑證式驗證]。 |
| 用戶端認證 | 您所具有的服務主體，已針對此應用程式或其所屬應用程式類別進行設定。 若要深入了解，請參閱[使用 Azure CLI 2.0 建立服務主體]。 |
| 受控服務識別 (MSI) | 您的應用程式正在 Azure 資源上執行，該資源已使用受控服務識別 (MSI) 進行設定。 若要深入了解，請參閱[適用於 Azure 資源的受控服務識別 (MSI)]。 |
| 裝置權杖 | 您的應用程式__只__適用於以互動方式使用，且可能會有來自多個 AAD 租用戶的多位使用者。 使用者可以存取網頁瀏覽器來登入。 若要深入了解，請參閱[使用裝置權杖驗證](#use-device-token-authentication)。|
| 使用者名稱/密碼 | 您所具有的應用程式無法使用任何其他驗證方法。 您的使用者未啟用 AAD 登入的多重要素驗證。 |

> [!IMPORTANT]
> 若您使用的是用戶端認證以外的驗證類型，則必須在 Azure Active Directory 中註冊您的應用程式。 若要了解註冊方式，請參閱[整合應用程式與 Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications)。

> [!NOTE]
> 除非您有特殊需求，請避免使用者名稱/密碼驗證。 在使用者式登入適用的情況下，通常可改用裝置權杖驗證。

[開始在 Azure Active Directory 中使用憑證式驗證]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[使用 Azure CLI 2.0 建立服務主體]: /cli/azure/create-an-azure-service-principal-azure-cli
[適用於 Azure 資源的受控服務識別 (MSI)]: /azure/active-directory/managed-service-identity/overview

這些驗證類型可以透過不同的驗證方法加以使用。 [環境式驗證](#use-environment-based-authentication)會直接從程式的環境讀取認證。 [檔案式驗證](#use-file-based-authentication)會載入包含服務主體認證的檔案。 [用戶端式驗證](#use-an-authentication-client)會使用 Go 程式碼中的物件，使得您要負責在程式執行期間提供認證。 最後，[裝置權杖驗證](#use-device-token-authentication)需要使用者透過網頁瀏覽器，用權杖以互動方式登入，而且不能搭配環境式或檔案式驗證。

所有驗證函式和類型在 `github.com/Azure/go-autorest/autorest/azure/auth` 套件中都有提供。

> [!NOTE]
> 除非您有特殊需求，請避免使用用戶端式驗證。 這種驗證方法會助長錯誤的作法。 尤其是使用用戶端式驗證，會讓人更容易想用硬式編碼寫入認證。 若未來的 SDK 版本驗證需求有所變更，撰寫的自訂驗證程式碼到時候也可能會失效。

## <a name="use-environment-based-authentication"></a>使用環境式驗證

若您是在嚴格控制的環境下執行您的應用程式 (例如在容器中)，環境式驗證是合理的選擇。 您會先設定 Shell 環境再執行應用程式，且 Go SDK 會在執行階段讀取這些環境變數，向 Azure 進行驗證。 

環境式驗證可支援除了裝置權杖以外的所有驗證方法，並以下列順序依次進行評估：用戶端認證、憑證、使用者名稱/密碼，最後是受控服務識別 (MSI)。 若未設定必要的環境變數或 SDK 遭到驗證服務拒絕，則會嘗試下一種驗證類型。 若 SDK 無法在環境中進行驗證，便會傳回錯誤。

下表詳細說明針對環境式驗證所支援的每種驗證類型，需要設定的環境變數。

| 驗證類型 | 環境變數 | 說明 |
| ------------------- | -------------------- | ----------- |
| __用戶端認證__ | `AZURE_TENANT_ID` | 服務主體所屬的 Active Directory 租用戶 ID。 |
| | `AZURE_CLIENT_ID` | 服務主體的名稱或 ID。 |
| | `AZURE_CLIENT_SECRET` | 與服務主體關聯的密碼。 |
| __憑證__ | `AZURE_TENANT_ID` | 憑證註冊的 Active Directory 租用戶 ID。 |
| | `AZURE_CLIENT_ID` | 與憑證相關聯的用戶端識別碼。 |
| | `AZURE_CERTIFICATE_PATH` | 用戶端憑證檔案的路徑。 |
| | `AZURE_CERTIFICATE_PASSWORD` | 用戶端憑證的密碼。 |
| __使用者名稱/密碼__ | `AZURE_TENANT_ID` | 使用者所屬的 Active Directory 租用戶 ID。 |
| | `AZURE_CLIENT_ID` | 應用程式用戶端識別碼。 |
| | `AZURE_USERNAME` | 用以登入的使用者名稱。 |
| | `AZURE_PASSWORD` | 用以登入的密碼。 |
| __MSI__ | | MSI 不需要設定任何認證， 但應用程式必須要在設定為使用 MSI 的 Azure 資源上執行。 欲知詳情，請參閱[適用於 Azure 資源的受控服務識別 (MSI)]。 |

若需要連線到預設 Azure 公用雲端以外的雲端或管理端點，您也可以設定下列環境變數。 最常需要設定這些環境變數的幾個原因是您使用了 Azure Stack、位於不同地理區域的雲端，或 Azure 傳統部署模型。

| 環境變數 | 說明  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | 要連線到的雲端環境名稱。 |
| `AZURE_AD_RESOURCE` | 連線時要使用的 Active Directory 資源 ID。 這應該是指向管理端點的 URI。 |

使用環境式驗證時，呼叫 [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) 函式可取得授權者物件。 然後在用戶端的 `Authorizer` 屬性上設定此物件，允許用戶端存取 Azure。

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

### <a name="authentication-on-azure-stack"></a>在 Azure Stack 上進行驗證

若要在 Azure Stack 上進行驗證，您需要設定以下變數：

| 環境變數 | 說明  |
|----------------------|--------------|
| `AZURE_AD_ENDPOINT` | Azure Active Directory 端點。 |
| `AZURE_AD_RESOURCE` | Active Directory 資源識別碼。 |

可以從 Azure Stack 中繼資料資訊中擷取這些變數。 若要擷取中繼資料，請在 Azure Stack 環境中開啟網路瀏覽器，並使用下列 URL：`(ResourceManagerURL)/metadata/endpoints?api-version=1.0`

`ResourceManagerURL` 會依 Azure Stack 部署的區域名稱、機器名稱和外部完整網域名稱 (FQDN) 而有所不同：

| 環境 | ResourceManagerURL |
|----------------------|--------------|
| 開發套件 | `https://management.local.azurestack.external/` |
| 整合系統 | `https://management.(region).ext-(machine-name).(FQDN)` |

如需如何在 Azure Stack 上使用 Azure SDK for Go 的詳細資訊，請參閱[在 Azure Stack 中搭配使用 Go 與 API 版本設定檔](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-version-profiles-go)


## <a name="use-file-based-authentication"></a>使用檔案式驗證

檔案式驗證只適用於由 [Azure CLI 2.0](/cli/azure) 所產生、以本機檔案格式儲存的用戶端認證。 以 `--sdk-auth` 參數建立新的服務主體時，您可以輕鬆地建立此檔案。 若您計劃使用檔案式驗證，請務必在建立服務主體時，提供這個引數。 因為 CLI 會將輸出列印到 `stdout`，請將輸出重新導向到檔案。

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

請將 `AZURE_AUTH_LOCATION` 環境變數設定成授權檔案所在的位置。 應用程式會讀取這個環境變數，並剖析其中的認證。 若您需要在執行階段取授權檔案，請使用 [os.Setenv](https://golang.org/pkg/os/#Setenv) 函式來操縱程式的環境。

若要載入驗證資訊，請呼叫 [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) 函式。 與環境式授權不同，檔案式授權需要資源端點。

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

如需有關使用服務主體，以及管理其存取權限的詳細資訊，請參閱[使用 Azure CLI 2.0 建立服務主體]。

## <a name="use-device-token-authentication"></a>使用裝置權杖驗證

若您想讓使用者以互動方式登入，提供這項功能最好的方式是透過裝置權杖驗證。 此驗證流程會傳遞使用者權杖，並貼入 Microsoft 登入網站，然後在此使用 Azure Active Directory (AAD) 帳戶登入。 這種驗證方法不同於使用者名稱/密碼驗證，可支援已啟用多重要素驗證的帳戶。

若要使用裝置權杖驗證，請使用 [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig) 函式建立 [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) 授權者。 呼叫結果物件上的 [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) 以開始驗證程序。 裝置流程驗證會封鎖程式執行，直到整個驗證流程完成為止。

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a>使用驗證用戶端

若您需要特定類型的驗證，並願意讓程式自行載入來自使用者的驗證資訊，可以使用任何符合 [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig) 介面的用戶端。 若您需要互動式程式、使用特定的組態檔，或因故無法使用其他驗證方法時，請使用可實作這個介面的驗證類型。

> [!WARNING]
> 請勿用硬式編碼將 Azure 認證寫入應用程式中。 將祕密放入應用程式二進位檔會使攻擊者易於擷取，無論該應用程式是否正在執行。 這會使獲得該認證授權的所有 Azure 資源暴露在風險下！

下表列出 SDK 中符合 `AuthorizerConfig` 介面的類型。

| 驗證類型 | 授權者類型 |
|---------------------|-----------------------|
| 憑證式驗證 | [ClientCertificateConfig] |
| 用戶端認證 | [ClientCredentialsConfig] |
| 受控服務識別 (MSI) | [MSIConfig] |
| 使用者名稱/密碼 | [UsernamePasswordConfig] |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

使用與其相關聯的 `New` 函式建立驗證器，然後再呼叫結果物件上的 `Authorize` 來執行驗證。 例如，使用憑證式驗證：

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
