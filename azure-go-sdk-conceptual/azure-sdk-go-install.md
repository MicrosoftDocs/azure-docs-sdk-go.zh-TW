---
title: 安裝 Azure SDK for Go
description: 如何安裝、Vendoring 處理及設定 Azure SDK for Go。
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/14/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 2799e3a6c637036eeaf7b20adf8aa55a8a4ab400
ms.sourcegitcommit: 4db332f5e43a5b43032ff9017805d5fd5a650d86
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55145527"
---
# <a name="install-the-azure-sdk-for-go"></a>安裝 Azure SDK for Go

歡迎使用 Azure SDK for Go！ 此 SDK 可讓您從 Go 應用程式管理 Azure 服務並且進行互動。

## <a name="get-the-azure-sdk-for-go"></a>取得 Azure SDK for Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

某些 Azure 服務本身具備 Go SDK，且並未包含在核心 Azure SDK for Go 套件中。 下表列出各項服務和服務本身的 SDK，以及套件名稱。 這些套件均為預覽狀態。

| 服務 | Package |
|---------|---------|
| Blob 儲存體 | [github.com/Azure/azure-storage-blob-go](https://github.com/Azure/azure-storage-blob-go) |
| 檔案儲存體 | [github.com/Azure/azure-storage-file-go](https://github.com/Azure/azure-storage-file-go) |
| 儲存體佇列 | [github.com/Azure/azure-storage-queue-go](https://github.com/Azure/azure-storage-queue-go) |
| 事件中樞 | [github.com/Azure/azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) |
| 服務匯流排 | [github.com/Azure/azure-service-bus-go](https://github.com/Azure/azure-service-bus-go) |
| Application Insights | [github.com/Microsoft/ApplicationInsights-go](https://github.com/Microsoft/ApplicationInsights-go) |

## <a name="vendor-the-azure-sdk-for-go"></a>販售 Azure SDK for Go

Azure SDK for Go 可以透過 [dep](https://github.com/golang/dep) 進行Vendoring 處理。 基於穩定性考量，建議進行 Vendoring 處理。 若要在您自己的專案中使用 `dep`，需將 `github.com/Azure/azure-sdk-for-go` 新增至 `Gopkg.toml` 的 `[[constraint]]` 區段。 例如，若要在版本 `14.0.0` 上進行 Vendoring 處理，請新增下列項目：

```toml
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a>在您的專案中包含 Azure SDK for Go

若要從 Go 程式碼使用 Azure 服務，請匯入任何與您互動的服務和所需的 `autorest` 模組。
您可從 GoDoc 取得[可用服務](https://godoc.org/github.com/Azure/azure-sdk-for-go)和 [AutoRest 套件](https://godoc.org/github.com/Azure/go-autorest)的完整可用模組清單。 您需要從 `go-autorest` 取得的最常見套件如下：

| Package | 說明 |
|---------|-------------|
| [github.com/Azure/go-autorest/autorest][autorest] | 用於處理服務用戶端驗證的物件 |
| [github.com/Azure/go-autorest/autorest/azure][autorest/azure] | 用來與 Azure 服務互動的常數 |
| [github.com/Azure/go-autorest/autorest/adal][autorest/adal] | 用於存取 Azure 服務的驗證機制 |
| [github.com/Azure/go-autorest/autorest/to][autorest/to] | 可供使用 Azure SDK 資料結構的類型判斷提示協助程式 |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

Go 套件與 Azure 服務會獨立建立版本。 服務版本是模組匯入路徑的一部份，位於 `services` 模組下方。 模組的完整路徑是服務名稱，後面接著 `YYYY-MM-DD` 格式的版本，其後再接著服務名稱。 例如，若要匯入 Compute 服務的 `2017-03-30` 版本：

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

建議您在開始開發時使用最新版的服務，並始終保持一致。
即使在 Go SDK 沒有更新版本的期間，服務需求還是可能因為服務版本不同而有所變更，進而破壞您的程式碼。

如果您需要服務的集體快照集，您也可以選取單一設定檔版本。 現在，只有鎖定的設定檔為版本 `2017-03-09`，其可能沒有服務的最新功能。 設定檔位於 `profiles` 模組之下，其版本為 `YYYY-MM-DD` 格式。 服務會分組歸類在其設定檔版本之下。 例如，若要從 `2017-03-09` 設定檔匯入 Azure 資源管理模組：

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> 另外還有 `preview` 和 `latest` 設定檔可用。 不建議使用這些設定檔。 這些設定檔是滾動版本，服務行為可能會隨時改變。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure SDK for Go，請嘗試快速入門。

* [利用範本部署虛擬機器](azure-sdk-go-qs-vm.md)
* [使用 Azure Blob SDK for Go 將物件傳送至 Azure Blob 儲存體](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [連線至 Azure Database for PostgreSQL](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

如果您想要立即在 Go SDK 中開始使用其他服務，請查看部分可用的範例程式碼。

* [使用 Azure 服務進行驗證](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/internal/iam)
* [使用 SSH 驗證部署新的虛擬機器](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [將容器映像部署至 Azure 容器執行個體](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [在 Azure Kubernetes 服務中建立叢集](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute)
* [使用 Azure 儲存體服務](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [Azure SDK for Go 的所有範例](https://github.com/azure-samples/azure-sdk-for-go-samples)
