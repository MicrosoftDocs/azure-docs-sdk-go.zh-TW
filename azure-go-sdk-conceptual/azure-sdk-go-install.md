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
ms.openlocfilehash: 8423b3fedd89e57662bf96f777a5b30926914da9
ms.sourcegitcommit: b81b17cbb934399c195bfdcb87137aee935f5234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34755510"
---
# <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="dcf67-103">安裝 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="dcf67-103">Install the Azure SDK for Go</span></span>

<span data-ttu-id="dcf67-104">歡迎使用 Azure SDK for Go！</span><span class="sxs-lookup"><span data-stu-id="dcf67-104">Welcome to the Azure SDK for Go!</span></span> <span data-ttu-id="dcf67-105">此 SDK 可讓您從 Go 應用程式管理 Azure 服務並且進行互動。</span><span class="sxs-lookup"><span data-stu-id="dcf67-105">The SDK allows you to manage and interact with Azure services from your Go applications.</span></span>

## <a name="get-the-azure-sdk-for-go"></a><span data-ttu-id="dcf67-106">取得 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="dcf67-106">Get the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

<span data-ttu-id="dcf67-107">某些 Azure 服務本身具備 Go SDK，且並未包含在核心 Azure SDK for Go 套件中。</span><span class="sxs-lookup"><span data-stu-id="dcf67-107">Some Azure services have their own Go SDK and aren't included in the core Azure SDK for Go package.</span></span> <span data-ttu-id="dcf67-108">下表列出各項服務和服務本身的 SDK，以及套件名稱。</span><span class="sxs-lookup"><span data-stu-id="dcf67-108">The following table lists the services with their own SDKs and their package names.</span></span> <span data-ttu-id="dcf67-109">這些套件均為預覽狀態。</span><span class="sxs-lookup"><span data-stu-id="dcf67-109">These packages are all considered to be in preview.</span></span>

| <span data-ttu-id="dcf67-110">服務</span><span class="sxs-lookup"><span data-stu-id="dcf67-110">Service</span></span> | <span data-ttu-id="dcf67-111">Package</span><span class="sxs-lookup"><span data-stu-id="dcf67-111">Package</span></span> |
|---------|---------|
| <span data-ttu-id="dcf67-112">Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="dcf67-112">Blob Storage</span></span> | [<span data-ttu-id="dcf67-113">github.com/Azure/azure-storage-blob-go</span><span class="sxs-lookup"><span data-stu-id="dcf67-113">github.com/Azure/azure-storage-blob-go</span></span>](https://github.com/Azure/azure-storage-blob-go) |
| <span data-ttu-id="dcf67-114">檔案儲存體</span><span class="sxs-lookup"><span data-stu-id="dcf67-114">File Storage</span></span> | [<span data-ttu-id="dcf67-115">github.com/Azure/azure-storage-file-go</span><span class="sxs-lookup"><span data-stu-id="dcf67-115">github.com/Azure/azure-storage-file-go</span></span>](https://github.com/Azure/azure-storage-file-go) |
| <span data-ttu-id="dcf67-116">事件中樞</span><span class="sxs-lookup"><span data-stu-id="dcf67-116">Event Hub</span></span> | [<span data-ttu-id="dcf67-117">github.com/Azure/azure-event-hubs-go</span><span class="sxs-lookup"><span data-stu-id="dcf67-117">github.com/Azure/azure-event-hubs-go</span></span>](https://github.com/Azure/azure-event-hubs-go) |
| <span data-ttu-id="dcf67-118">Application Insights</span><span class="sxs-lookup"><span data-stu-id="dcf67-118">Application Insights</span></span> | [<span data-ttu-id="dcf67-119">github.com/Microsoft/ApplicationInsights-go</span><span class="sxs-lookup"><span data-stu-id="dcf67-119">github.com/Microsoft/ApplicationInsights-go</span></span>](https://github.com/Microsoft/ApplicationInsights-go) |

## <a name="vendor-the-azure-sdk-for-go"></a><span data-ttu-id="dcf67-120">販售 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="dcf67-120">Vendor the Azure SDK for Go</span></span>

<span data-ttu-id="dcf67-121">Azure SDK for Go 可以透過 [dep](https://github.com/golang/dep) 進行Vendoring 處理。</span><span class="sxs-lookup"><span data-stu-id="dcf67-121">The Azure SDK for Go may be vendored through [dep](https://github.com/golang/dep).</span></span> <span data-ttu-id="dcf67-122">基於穩定性考量，建議進行 Vendoring 處理。</span><span class="sxs-lookup"><span data-stu-id="dcf67-122">For stability reasons, vendoring is recommended.</span></span> <span data-ttu-id="dcf67-123">若要使用 `dep` 支援，請將 `github.com/Azure/azure-sdk-for-go` 新增至 `Gopkg.toml` 的 `[[constraint]]` 區段。</span><span class="sxs-lookup"><span data-stu-id="dcf67-123">In order to use `dep` support, add `github.com/Azure/azure-sdk-for-go` to a `[[constraint]]` section of your `Gopkg.toml`.</span></span> <span data-ttu-id="dcf67-124">例如，若要在版本 `14.0.0` 上進行 Vendoring 處理，請新增下列項目：</span><span class="sxs-lookup"><span data-stu-id="dcf67-124">For example, to vendor on version `14.0.0`, add the following entry:</span></span>

```
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a><span data-ttu-id="dcf67-125">在您的專案中包含 Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="dcf67-125">Include the Azure SDK for Go in your project</span></span>

<span data-ttu-id="dcf67-126">若要從 Go 程式碼使用 Azure 服務，請匯入任何與您互動的服務和所需的 `autorest` 模組。</span><span class="sxs-lookup"><span data-stu-id="dcf67-126">To use Azure services from your Go code, import any services you interact with and the required `autorest` modules.</span></span>
<span data-ttu-id="dcf67-127">您可從 GoDoc 取得[可用服務](https://godoc.org/github.com/Azure/azure-sdk-for-go)和 [AutoRest 套件](https://godoc.org/github.com/Azure/go-autorest)的完整可用模組清單。</span><span class="sxs-lookup"><span data-stu-id="dcf67-127">You get a complete list of the available modules from GoDoc for [available services](https://godoc.org/github.com/Azure/azure-sdk-for-go) and [AutoRest packages](https://godoc.org/github.com/Azure/go-autorest).</span></span> <span data-ttu-id="dcf67-128">您需要從 `go-autorest` 取得的最常見套件如下：</span><span class="sxs-lookup"><span data-stu-id="dcf67-128">The most common packages you need from `go-autorest` are:</span></span>

| <span data-ttu-id="dcf67-129">Package</span><span class="sxs-lookup"><span data-stu-id="dcf67-129">Package</span></span> | <span data-ttu-id="dcf67-130">說明</span><span class="sxs-lookup"><span data-stu-id="dcf67-130">Description</span></span> |
|---------|-------------|
| <span data-ttu-id="dcf67-131">[github.com/Azure/go-autorest/autorest][autorest]</span><span class="sxs-lookup"><span data-stu-id="dcf67-131">[github.com/Azure/go-autorest/autorest][autorest]</span></span> | <span data-ttu-id="dcf67-132">用於處理服務用戶端驗證的物件</span><span class="sxs-lookup"><span data-stu-id="dcf67-132">Objects for handling service client authentication</span></span> |
| <span data-ttu-id="dcf67-133">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span><span class="sxs-lookup"><span data-stu-id="dcf67-133">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span></span> | <span data-ttu-id="dcf67-134">用來與 Azure 服務互動的常數</span><span class="sxs-lookup"><span data-stu-id="dcf67-134">Constants for interactions with Azure services</span></span> |
| <span data-ttu-id="dcf67-135">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span><span class="sxs-lookup"><span data-stu-id="dcf67-135">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span></span> | <span data-ttu-id="dcf67-136">用於存取 Azure 服務的驗證機制</span><span class="sxs-lookup"><span data-stu-id="dcf67-136">Authentication mechanisms for accessing Azure services</span></span> |
| <span data-ttu-id="dcf67-137">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span><span class="sxs-lookup"><span data-stu-id="dcf67-137">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span></span> | <span data-ttu-id="dcf67-138">可供使用 Azure SDK 資料結構的類型判斷提示協助程式</span><span class="sxs-lookup"><span data-stu-id="dcf67-138">Type assertion helpers for working with Azure SDK data structures</span></span> |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

<span data-ttu-id="dcf67-139">Azure 服務的模組與其 SDK API 獨立設定版本。</span><span class="sxs-lookup"><span data-stu-id="dcf67-139">Modules for Azure services are versioned independently from the SDK APIs for them.</span></span> <span data-ttu-id="dcf67-140">這些版本屬於模組匯入路徑的一部分，且來自「服務版本」或「設定檔」。</span><span class="sxs-lookup"><span data-stu-id="dcf67-140">These versions are part of the module import path, and are from either a _service version_ or a _profile_.</span></span> <span data-ttu-id="dcf67-141">目前建議您使用特定服務版本進行開發和發行。</span><span class="sxs-lookup"><span data-stu-id="dcf67-141">Currently, it is recommended that you use a specific service version for both development and release.</span></span> <span data-ttu-id="dcf67-142">服務位於 `services` 模組之下。</span><span class="sxs-lookup"><span data-stu-id="dcf67-142">Services are located under the `services` module.</span></span> <span data-ttu-id="dcf67-143">匯入的完整路徑是服務名稱，後面接著 `YYYY-MM-DD` 格式的版本，其後再接著服務名稱。</span><span class="sxs-lookup"><span data-stu-id="dcf67-143">The full path for the import is the name of the service, followed by the version in `YYYY-MM-DD` format, followed by the service name again.</span></span> <span data-ttu-id="dcf67-144">例如，若要納入計算服務的 `2017-03-30` 版本：</span><span class="sxs-lookup"><span data-stu-id="dcf67-144">For example, to include the `2017-03-30` version of the Compute service:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

<span data-ttu-id="dcf67-145">除非您有理由採用別種做法，否則建議您使用最新版的服務。</span><span class="sxs-lookup"><span data-stu-id="dcf67-145">Right now it is recommended that you use the latest version of a service, unless you have a reason to do otherwise.</span></span>

<span data-ttu-id="dcf67-146">如果您需要服務的集體快照集，您也可以選取單一設定檔版本。</span><span class="sxs-lookup"><span data-stu-id="dcf67-146">If you need a collective snapshot of services, you can also select a single profile version.</span></span> <span data-ttu-id="dcf67-147">現在，只有鎖定的設定檔為版本 `2017-03-09`，其可能沒有服務的最新功能。</span><span class="sxs-lookup"><span data-stu-id="dcf67-147">Right now, the only locked profile is version `2017-03-09`, which may not have the latest features of services.</span></span> <span data-ttu-id="dcf67-148">設定檔位於 `profiles` 模組之下，其版本為 `YYYY-MM-DD` 格式。</span><span class="sxs-lookup"><span data-stu-id="dcf67-148">Profiles are located under the `profiles` module, with their version in the `YYYY-MM-DD` format.</span></span> <span data-ttu-id="dcf67-149">服務會分組歸類在其設定檔版本之下。</span><span class="sxs-lookup"><span data-stu-id="dcf67-149">Services are grouped under their profile version.</span></span> <span data-ttu-id="dcf67-150">例如，若要從 `2017-03-09` 設定檔匯入 Azure 資源管理模組：</span><span class="sxs-lookup"><span data-stu-id="dcf67-150">For example, to import the Azure Resources management module from the `2017-03-09` profile:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> <span data-ttu-id="dcf67-151">另外還有 `preview` 和 `latest` 設定檔可用。</span><span class="sxs-lookup"><span data-stu-id="dcf67-151">There are also `preview` and `latest` profiles available.</span></span> <span data-ttu-id="dcf67-152">不建議使用這些設定檔。</span><span class="sxs-lookup"><span data-stu-id="dcf67-152">Using them is not recommended.</span></span> <span data-ttu-id="dcf67-153">這些設定檔是滾動版本，服務行為可能會隨時改變。</span><span class="sxs-lookup"><span data-stu-id="dcf67-153">These profiles are rolling versions and service behavior may change at any time.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dcf67-154">後續步驟</span><span class="sxs-lookup"><span data-stu-id="dcf67-154">Next steps</span></span>

<span data-ttu-id="dcf67-155">若要開始使用 Azure SDK for Go，請嘗試快速入門。</span><span class="sxs-lookup"><span data-stu-id="dcf67-155">To begin using the Azure SDK for Go, try out a quickstart.</span></span>

* [<span data-ttu-id="dcf67-156">利用範本部署虛擬機器</span><span class="sxs-lookup"><span data-stu-id="dcf67-156">Deploy a virtual machine from a template</span></span>](azure-sdk-go-qs-vm.md)
* [<span data-ttu-id="dcf67-157">使用 Azure Blob SDK for Go 將物件傳送至 Azure Blob 儲存體</span><span class="sxs-lookup"><span data-stu-id="dcf67-157">Transfer objects to Azure Blob Storage with the Azure Blob SDK for Go</span></span>](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [<span data-ttu-id="dcf67-158">連線至 Azure Database for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="dcf67-158">Connect to Azure Database for PostgreSQL</span></span>](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

<span data-ttu-id="dcf67-159">如果您想要立即在 Go SDK 中開始使用其他服務，請查看部分可用的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="dcf67-159">If you want to get started with other services in the Go SDK immediately, take a look at some of the available sample code.</span></span>

* [<span data-ttu-id="dcf67-160">使用 Azure 服務進行驗證</span><span class="sxs-lookup"><span data-stu-id="dcf67-160">Authenticate with Azure services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/iam)
* [<span data-ttu-id="dcf67-161">使用 SSH 驗證部署新的虛擬機器</span><span class="sxs-lookup"><span data-stu-id="dcf67-161">Deploy new virtual machines with SSH authentication</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [<span data-ttu-id="dcf67-162">將容器映像部署至 Azure 容器執行個體</span><span class="sxs-lookup"><span data-stu-id="dcf67-162">Deploy a container image to Azure Container Instances</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerinstance)
* [<span data-ttu-id="dcf67-163">在 Azure Kubernetes 服務中建立叢集</span><span class="sxs-lookup"><span data-stu-id="dcf67-163">Create a cluster in Azure Kubernetes Service</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerservice)
* [<span data-ttu-id="dcf67-164">使用 Azure 儲存體服務</span><span class="sxs-lookup"><span data-stu-id="dcf67-164">Work with Azure Storage services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [<span data-ttu-id="dcf67-165">Azure SDK for Go 的所有範例</span><span class="sxs-lookup"><span data-stu-id="dcf67-165">All samples for the Azure SDK for Go</span></span>](https://github.com/azure-samples/azure-sdk-for-go-samples)
