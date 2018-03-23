---
title: 適用於 Go 開發人員的工具
description: 可供搭配 Azure SDK for Go 運作的工具，以及 Azure 服務
author: sptramer
ms.author: sttramer
ms.date: 01/30/2018
ms.topic: article
ms.devlang: go
manager: carmonm
ms.openlocfilehash: 054965eb1ea4f1a7556e2968dfbe07b2db69d26f
ms.sourcegitcommit: fcc1786d59d2e32c97a9a8e0748e06f564a961bd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a><span data-ttu-id="1ae8b-103">使用 Azure SDK for Go 的開發人員所適用的工具</span><span class="sxs-lookup"><span data-stu-id="1ae8b-103">Tools for developers using the Azure SDK for Go</span></span>

<span data-ttu-id="1ae8b-104">為了能有效率地撰寫 Go 程式碼並且讓它與 Azure 服務完美配合運作，以下有一些建議的工具。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-104">To be effective at writing Go code and have it work seamlessly with Azure services, here are some recommended tools.</span></span>

## <a name="azure-cli-20"></a><span data-ttu-id="1ae8b-105">Azure CLI 2.0</span><span class="sxs-lookup"><span data-stu-id="1ae8b-105">Azure CLI 2.0</span></span>

<span data-ttu-id="1ae8b-106">Azure 2.0 CLI 提供的命令列介面可建立及設定您訂用帳戶中的 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-106">The Azure 2.0 CLI provides a command-line interface to create and configure Azure resources in your subscriptions.</span></span> <span data-ttu-id="1ae8b-107">CLI 可協助您開始快速建置一般和共用 Azure 資源，讓您可以專注於更複雜的服務使用方式。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-107">The CLI can help you get started building common and shared Azure resources quickly, so that you can focus on more complex usage of services.</span></span> <span data-ttu-id="1ae8b-108">CLI 具有查詢和篩選功能，因此您可以直接將輸出輸送到您最愛的命令列工具。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-108">The CLI has query and filtering features so you can pipe output directly to your favorite command-line tools.</span></span> <span data-ttu-id="1ae8b-109">CLI 可以 Docker 映像形式，或透過 [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) 安裝在您的本機系統上。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-109">The CLI is available for installation on your local system, as a Docker image, or through [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1ae8b-110">安裝 Azure CLI 2.0</span><span class="sxs-lookup"><span data-stu-id="1ae8b-110">Install the Azure CLI 2.0</span></span>](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a><span data-ttu-id="1ae8b-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ae8b-111">Visual Studio Code</span></span>

<span data-ttu-id="1ae8b-112">Visual Studio Code 是一個輕量級編輯器，可透過擴充功能完整支援 Go 語言。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-112">Visual Studio Code is a lightweight editor that has comprehensive support for the Go language through extensions.</span></span> <span data-ttu-id="1ae8b-113">這些擴充功能包含支援自動完成、`impl` 範本、重構和偵錯等功能。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-113">These extensions include support for features like autocomplete, `impl` templates, refactoring, and debugging.</span></span> <span data-ttu-id="1ae8b-114">Visual Studio Code 還為一般開發人員工具提供許多擴充功能 (例如原始檔控制)，甚至提供可與 Azure 服務進行直接互動的擴充功能。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-114">Visual Studio Code also offers many extensions for common developer tools such as source control, and even offers extensions for direct interactions with Azure services.</span></span> <span data-ttu-id="1ae8b-115">Microsoft 會維護包含這些 Azure 擴充功能的一項正式中繼擴充功能，包括 Azure CLI 的互動式介面。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-115">Microsoft maintains an official meta-extension including these Azure extensions, including an interactive interface for the Azure CLI.</span></span>

* [<span data-ttu-id="1ae8b-116">安裝 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ae8b-116">Install Visual Studio Code</span></span>](https://code.visualstudio.com/Download)
* [<span data-ttu-id="1ae8b-117">取得 Visual Studio Code Go 擴充功能</span><span class="sxs-lookup"><span data-stu-id="1ae8b-117">Get the Visual Studio Code Go extension</span></span>](https://code.visualstudio.com/docs/languages/go)
* [<span data-ttu-id="1ae8b-118">取得 Azure Tools 擴充功能</span><span class="sxs-lookup"><span data-stu-id="1ae8b-118">Get the Azure Tools extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="dependency-management-with-dep"></a><span data-ttu-id="1ae8b-119">使用 dep 進行相依性管理</span><span class="sxs-lookup"><span data-stu-id="1ae8b-119">Dependency management with dep</span></span>

<span data-ttu-id="1ae8b-120">因為還沒有正式的解決方案，所以有許多方法可使用 Go 來管理套件相依性並進行 Vendoring 處理。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-120">There are many ways to manage your package dependencies and do vendoring with Go, since there is no official solution yet.</span></span> <span data-ttu-id="1ae8b-121">執行此管理的建議方法是使用 `dep` 相依性管理員。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-121">The recommended way to perform this management is with the `dep` dependency manager.</span></span> <span data-ttu-id="1ae8b-122">Azure SDK for Go 會使用 dep 進行其 Vendoring 處理，並保證可使用 dep 正確取得其他任何專案的相依性。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-122">The Azure SDK for Go uses dep for its vendoring, and is guaranteed to correctly get dependencies for any other project using dep.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1ae8b-123">取得 dep 相依性管理員</span><span class="sxs-lookup"><span data-stu-id="1ae8b-123">Get the dep dependency manager</span></span>](https://github.com/tools/godep)

## <a name="telemetry-with-application-insights"></a><span data-ttu-id="1ae8b-124">使用 Application Insights 進行遙測</span><span class="sxs-lookup"><span data-stu-id="1ae8b-124">Telemetry with Application Insights</span></span>

<span data-ttu-id="1ae8b-125">[Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) 是一項分析產品，可讓您輕鬆地從應用程式中收集遙測資訊，以及與 Azure 生態系統、Visual Studio Team Services 和 GitHub 整合。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-125">[Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) is an analytics product that allows you to easily collect telemetry information from your applications and integrates with the Azure ecosystem, Visual Studio Team Services, and GitHub.</span></span> <span data-ttu-id="1ae8b-126">該產品使用於許多應用程式，而 Microsoft 提供 Go SDK 來搭配 Application Insights 運作。</span><span class="sxs-lookup"><span data-stu-id="1ae8b-126">It can be used in many applications, and Microsoft offers a Go SDK for working with Application Insights.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1ae8b-127">取得 Application Insights for Go SDK</span><span class="sxs-lookup"><span data-stu-id="1ae8b-127">Get the Application Insights for Go SDK</span></span>](https://github.com/Microsoft/ApplicationInsights-Go) 
