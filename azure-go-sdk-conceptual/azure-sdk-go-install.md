---
title: 安裝 Azure SDK for Go
description: 如何安裝、Vendoring 處理及設定 Azure SDK for Go。
author: sptramer
ms.author: sttramer
ms.date: 03/14/2018
ms.topic: article
ms.devlang: go
manager: carmonm
ms.openlocfilehash: a6a92e080aea1a92f47a9d7083f133ca05a47541
ms.sourcegitcommit: 26520a8c6e812facb5b9432d68c370fa23c99888
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="582d7-103">安裝 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="582d7-103">Install the Azure SDK for Go</span></span>

<span data-ttu-id="582d7-104">歡迎使用 Azure SDK for Go！</span><span class="sxs-lookup"><span data-stu-id="582d7-104">Welcome to the Azure SDK for Go!</span></span> <span data-ttu-id="582d7-105">此 SDK 可讓您從 Go 應用程式管理 Azure 服務並且進行互動。</span><span class="sxs-lookup"><span data-stu-id="582d7-105">The SDK allows you to manage and interact with Azure services from your Go applications.</span></span>

## <a name="get-the-azure-sdk-for-go"></a><span data-ttu-id="582d7-106">取得 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="582d7-106">Get the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

<span data-ttu-id="582d7-107">使用 Azure 儲存體 Blob 時需要個別的 SDK。</span><span class="sxs-lookup"><span data-stu-id="582d7-107">Working with Azure Storage Blobs requires a separate SDK.</span></span>

```bash
go get -u -d github.com/Azure/azure-storage-blob-go/...
```

## <a name="vendor-the-azure-sdk-for-go"></a><span data-ttu-id="582d7-108">販售 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="582d7-108">Vendor the Azure SDK for Go</span></span>

<span data-ttu-id="582d7-109">Azure SDK for Go 可以透過 [dep](https://github.com/golang/dep) 進行Vendoring 處理。</span><span class="sxs-lookup"><span data-stu-id="582d7-109">The Azure SDK for Go may be vendored through [dep](https://github.com/golang/dep).</span></span> <span data-ttu-id="582d7-110">基於穩定性考量，建議進行 Vendoring 處理。</span><span class="sxs-lookup"><span data-stu-id="582d7-110">For stability reasons, vendoring is recommended.</span></span> <span data-ttu-id="582d7-111">若要使用 `dep` 支援，請將 `github.com/Azure/azure-sdk-for-go` 新增至 `Gopkg.toml` 的 `[[constraint]]` 區段。</span><span class="sxs-lookup"><span data-stu-id="582d7-111">In order to use `dep` support, add `github.com/Azure/azure-sdk-for-go` to a `[[constraint]]` section of your `Gopkg.toml`.</span></span> <span data-ttu-id="582d7-112">例如，若要在版本 `14.0.0` 上進行 Vendoring 處理，請新增下列項目：</span><span class="sxs-lookup"><span data-stu-id="582d7-112">For example, to vendor on version `14.0.0`, add the following entry:</span></span>

```
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a><span data-ttu-id="582d7-113">在您的專案中包含 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="582d7-113">Include the Azure SDK for Go in your project</span></span>

<span data-ttu-id="582d7-114">若要從 Go 程式碼使用 Azure 服務，請匯入任何與您互動的服務和所需的 `autorest` 模組。</span><span class="sxs-lookup"><span data-stu-id="582d7-114">To use Azure services from your Go code, import any services you interact with and the required `autorest` modules.</span></span>
<span data-ttu-id="582d7-115">您可從 GoDoc 取得[可用服務](https://godoc.org/github.com/Azure/azure-sdk-for-go)和 [AutoRest 套件](https://godoc.org/github.com/Azure/go-autorest)的完整可用模組清單。</span><span class="sxs-lookup"><span data-stu-id="582d7-115">You get a complete list of the available modules from GoDoc for [available services](https://godoc.org/github.com/Azure/azure-sdk-for-go) and [AutoRest packages](https://godoc.org/github.com/Azure/go-autorest).</span></span> <span data-ttu-id="582d7-116">您需要從 `go-autorest` 取得的最常見套件如下：</span><span class="sxs-lookup"><span data-stu-id="582d7-116">The most common packages you need from `go-autorest` are:</span></span>

| <span data-ttu-id="582d7-117">Package</span><span class="sxs-lookup"><span data-stu-id="582d7-117">Package</span></span> | <span data-ttu-id="582d7-118">說明</span><span class="sxs-lookup"><span data-stu-id="582d7-118">Description</span></span> |
|---------|-------------|
| <span data-ttu-id="582d7-119">[github.com/Azure/go-autorest/autorest][autorest]</span><span class="sxs-lookup"><span data-stu-id="582d7-119">[github.com/Azure/go-autorest/autorest][autorest]</span></span> | <span data-ttu-id="582d7-120">用於處理服務用戶端驗證的物件</span><span class="sxs-lookup"><span data-stu-id="582d7-120">Objects for handling service client authentication</span></span> |
| <span data-ttu-id="582d7-121">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span><span class="sxs-lookup"><span data-stu-id="582d7-121">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span></span> | <span data-ttu-id="582d7-122">用來與 Azure 服務互動的常數</span><span class="sxs-lookup"><span data-stu-id="582d7-122">Constants for interactions with Azure services</span></span> |
| <span data-ttu-id="582d7-123">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span><span class="sxs-lookup"><span data-stu-id="582d7-123">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span></span> | <span data-ttu-id="582d7-124">用於存取 Azure 服務的驗證機制</span><span class="sxs-lookup"><span data-stu-id="582d7-124">Authentication mechanisms for accessing Azure services</span></span> |
| <span data-ttu-id="582d7-125">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span><span class="sxs-lookup"><span data-stu-id="582d7-125">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span></span> | <span data-ttu-id="582d7-126">可供使用 Azure SDK 資料結構的類型判斷提示協助程式</span><span class="sxs-lookup"><span data-stu-id="582d7-126">Type assertion helpers for working with Azure SDK data structures</span></span> |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

<span data-ttu-id="582d7-127">Azure 服務的模組與其 SDK API 獨立設定版本。</span><span class="sxs-lookup"><span data-stu-id="582d7-127">Modules for Azure services are versioned independently from the SDK APIs for them.</span></span> <span data-ttu-id="582d7-128">這些版本屬於模組匯入路徑的一部分，且來自「服務版本」或「設定檔」。</span><span class="sxs-lookup"><span data-stu-id="582d7-128">These versions are part of the module import path, and are from either a _service version_ or a _profile_.</span></span> <span data-ttu-id="582d7-129">目前建議您使用特定服務版本進行開發和發行。</span><span class="sxs-lookup"><span data-stu-id="582d7-129">Currently, it is recommended that you use a specific service version for both development and release.</span></span> <span data-ttu-id="582d7-130">服務位於 `services` 模組之下。</span><span class="sxs-lookup"><span data-stu-id="582d7-130">Services are located under the `services` module.</span></span> <span data-ttu-id="582d7-131">匯入的完整路徑是服務名稱，後面接著 `YYYY-MM-DD` 格式的版本，其後再接著服務名稱。</span><span class="sxs-lookup"><span data-stu-id="582d7-131">The full path for the import is the name of the service, followed by the version in `YYYY-MM-DD` format, followed by the service name again.</span></span> <span data-ttu-id="582d7-132">例如，若要納入計算服務的 `2017-03-30` 版本：</span><span class="sxs-lookup"><span data-stu-id="582d7-132">For example, to include the `2017-03-30` version of the Compute service:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

<span data-ttu-id="582d7-133">除非您有理由採用別種做法，否則建議您使用最新版的服務。</span><span class="sxs-lookup"><span data-stu-id="582d7-133">Right now it is recommended that you use the latest version of a service, unless you have a reason to do otherwise.</span></span>

<span data-ttu-id="582d7-134">如果您需要服務的集體快照集，您也可以選取單一設定檔版本。</span><span class="sxs-lookup"><span data-stu-id="582d7-134">If you need a collective snapshot of services, you can also select a single profile version.</span></span> <span data-ttu-id="582d7-135">現在，只有鎖定的設定檔為版本 `2017-03-30`，其可能沒有服務的最新功能。</span><span class="sxs-lookup"><span data-stu-id="582d7-135">Right now, the only locked profile is version `2017-03-30`, which may not have the latest features of services.</span></span> <span data-ttu-id="582d7-136">設定檔位於 `profiles` 模組之下，其版本為 `YYYY-MM-DD` 格式。</span><span class="sxs-lookup"><span data-stu-id="582d7-136">Profiles are located under the `profiles` module, with their version in the `YYYY-MM-DD` format.</span></span> <span data-ttu-id="582d7-137">服務會分組歸類在其設定檔版本之下。</span><span class="sxs-lookup"><span data-stu-id="582d7-137">Services are grouped under their profile version.</span></span> <span data-ttu-id="582d7-138">例如，若要從 `2017-03-09` 設定檔匯入 Azure 資源管理模組：</span><span class="sxs-lookup"><span data-stu-id="582d7-138">For example, to import the Azure Resources management module from the `2017-03-09` profile:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> <span data-ttu-id="582d7-139">另外還有 `preview` 和 `latest` 設定檔可用。</span><span class="sxs-lookup"><span data-stu-id="582d7-139">There are also `preview` and `latest` profiles available.</span></span> <span data-ttu-id="582d7-140">不建議使用這些設定檔。</span><span class="sxs-lookup"><span data-stu-id="582d7-140">Using them is not recommended.</span></span> <span data-ttu-id="582d7-141">這些設定檔是滾動版本，服務行為可能會隨時改變。</span><span class="sxs-lookup"><span data-stu-id="582d7-141">These profiles are rolling versions and service behavior may change at any time.</span></span>

## <a name="next-steps"></a><span data-ttu-id="582d7-142">後續步驟</span><span class="sxs-lookup"><span data-stu-id="582d7-142">Next steps</span></span>

<span data-ttu-id="582d7-143">若要開始使用 Azure SDK for Go，請嘗試快速入門。</span><span class="sxs-lookup"><span data-stu-id="582d7-143">To begin using the Azure SDK for Go, try out a quickstart.</span></span>

* [<span data-ttu-id="582d7-144">利用範本部署虛擬機器</span><span class="sxs-lookup"><span data-stu-id="582d7-144">Deploy a virtual machine from a template</span></span>](azure-sdk-go-qs-vm.md)
* [<span data-ttu-id="582d7-145">使用 Azure Blob SDK for Go 將物件傳送至 Azure Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="582d7-145">Transfer objects to Azure Blob Storage with the Azure Blob SDK for Go</span></span>](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [<span data-ttu-id="582d7-146">連線至 Azure Database for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="582d7-146">Connect to Azure Database for PostgreSQL</span></span>](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

<span data-ttu-id="582d7-147">如果您想要立即在 Go SDK 中開始使用其他服務，請查看部分可用的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="582d7-147">If you want to get started with other services in the Go SDK immediately, take a look at some of the available sample code.</span></span>

* [<span data-ttu-id="582d7-148">使用 Azure 服務進行驗證</span><span class="sxs-lookup"><span data-stu-id="582d7-148">Authenticate with Azure services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/iam)
* [<span data-ttu-id="582d7-149">使用 SSH 驗證部署新的虛擬機器</span><span class="sxs-lookup"><span data-stu-id="582d7-149">Deploy new virtual machines with SSH authentication</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [<span data-ttu-id="582d7-150">將容器映像部署至 Azure 容器執行個體</span><span class="sxs-lookup"><span data-stu-id="582d7-150">Deploy a container image to Azure Container Instances</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerinstance)
* [<span data-ttu-id="582d7-151">在 Azure Kubernetes 服務中建立叢集</span><span class="sxs-lookup"><span data-stu-id="582d7-151">Create a cluster in Azure Kubernetes Service</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerservice)
* [<span data-ttu-id="582d7-152">使用 Azure 儲存體服務</span><span class="sxs-lookup"><span data-stu-id="582d7-152">Work with Azure Storage services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [<span data-ttu-id="582d7-153">Azure SDK for Go 的所有範例</span><span class="sxs-lookup"><span data-stu-id="582d7-153">All samples for the Azure SDK for Go</span></span>](https://github.com/azure-samples/azure-sdk-for-go-samples)
