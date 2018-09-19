---
title: 使用 Azure SDK for Go 的開發人員所適用的工具
description: 可供搭配 Azure SDK for Go 運作的工具，以及 Azure 服務
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 70cf7d645f47df29e8e42599a0acd75858144783
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059198"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a><span data-ttu-id="97537-103">使用 Azure SDK for Go 的開發人員所適用的工具</span><span class="sxs-lookup"><span data-stu-id="97537-103">Tools for developers using the Azure SDK for Go</span></span>

<span data-ttu-id="97537-104">為了能有效率地撰寫 Go 程式碼並且讓它與 Azure 服務完美配合運作，以下有一些建議的工具。</span><span class="sxs-lookup"><span data-stu-id="97537-104">To be effective at writing Go code and have it work seamlessly with Azure services, here are some recommended tools.</span></span>

## <a name="azure-cli"></a><span data-ttu-id="97537-105">Azure CLI</span><span class="sxs-lookup"><span data-stu-id="97537-105">Azure CLI</span></span>

<span data-ttu-id="97537-106">Azure CLI 提供的命令列介面可建立及設定您訂用帳戶中的 Azure 資源。</span><span class="sxs-lookup"><span data-stu-id="97537-106">The Azure CLI provides a command-line interface to create and configure Azure resources in your subscriptions.</span></span> <span data-ttu-id="97537-107">CLI 可協助您開始快速建置一般和共用 Azure 資源，讓您可以專注於更複雜的服務使用方式。</span><span class="sxs-lookup"><span data-stu-id="97537-107">The CLI can help you get started building common and shared Azure resources quickly, so that you can focus on more complex usage of services.</span></span> <span data-ttu-id="97537-108">CLI 具有查詢和篩選功能，因此您可以直接將輸出輸送到您最愛的命令列工具。</span><span class="sxs-lookup"><span data-stu-id="97537-108">The CLI has query and filtering features so you can pipe output directly to your favorite command-line tools.</span></span> <span data-ttu-id="97537-109">CLI 可以 Docker 映像形式，或透過 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 安裝在您的本機系統上。</span><span class="sxs-lookup"><span data-stu-id="97537-109">The CLI is available for installation on your local system, as a Docker image, or through [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="97537-110">安裝 Azure CLI</span><span class="sxs-lookup"><span data-stu-id="97537-110">Install the Azure CLI</span></span>](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a><span data-ttu-id="97537-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97537-111">Visual Studio Code</span></span>

<span data-ttu-id="97537-112">Visual Studio Code 是輕量型編輯器，可提供 Go 支援。</span><span class="sxs-lookup"><span data-stu-id="97537-112">Visual Studio Code is a lightweight editor that offers Go support.</span></span> <span data-ttu-id="97537-113">此擴充功能提供自動完成、`impl` 範本、重構和偵錯等功能。</span><span class="sxs-lookup"><span data-stu-id="97537-113">This extension offers features like autocomplete, `impl` templates, refactoring, and debugging.</span></span> <span data-ttu-id="97537-114">Visual Studio Code 也支援從編輯器內存取原始檔控制，並提供可與 Azure 服務搭配使用的擴充功能。</span><span class="sxs-lookup"><span data-stu-id="97537-114">Visual Studio Code also offers support for in-editor access to source control, and extensions for working with Azure services.</span></span>

* [<span data-ttu-id="97537-115">安裝 Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="97537-115">Install Visual Studio Code</span></span>](https://code.visualstudio.com/Download)
* [<span data-ttu-id="97537-116">取得 Visual Studio Code Go 擴充功能</span><span class="sxs-lookup"><span data-stu-id="97537-116">Get the Visual Studio Code Go extension</span></span>](https://code.visualstudio.com/docs/languages/go)
* [<span data-ttu-id="97537-117">取得 Visual Studio Code Azure Tools 擴充功能</span><span class="sxs-lookup"><span data-stu-id="97537-117">Get the Visual Studio Code Azure Tools extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a><span data-ttu-id="97537-118">CI/CD 搭配 Azure DevOps Project</span><span class="sxs-lookup"><span data-stu-id="97537-118">CI/CD with Azure DevOps Project</span></span>

<span data-ttu-id="97537-119">Azure DevOps Project 管線可讓您為 Go 應用程式設定持續整合系統。</span><span class="sxs-lookup"><span data-stu-id="97537-119">Azure DevOps Project pipelines allow you to set up a continuous integration system for your Go applications.</span></span> <span data-ttu-id="97537-120">只要有 Git 存放庫，您就可以直接在 Azure 上進行部署和測試。</span><span class="sxs-lookup"><span data-stu-id="97537-120">All it takes is a git repo, and you can deploy and test directly on Azure.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="97537-121">了解如何利用 Azure DevOps Projects 建立 CI/CD 管道</span><span class="sxs-lookup"><span data-stu-id="97537-121">Learn how to create a CI/CD pipeline with Azure DevOps Project</span></span>](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a><span data-ttu-id="97537-122">使用 dep 進行相依性管理</span><span class="sxs-lookup"><span data-stu-id="97537-122">Dependency management with dep</span></span>

<span data-ttu-id="97537-123">Azure SDK for Go 會使用 dep 進行相依性管理。</span><span class="sxs-lookup"><span data-stu-id="97537-123">The Azure SDK for Go uses dep for dependency management.</span></span> <span data-ttu-id="97537-124">dep 命令可讓您提取 Go 應用程式的需求，並將其放到 vendor 目錄下，以避免發生版本衝突，並確保您的專案運作正常。</span><span class="sxs-lookup"><span data-stu-id="97537-124">The dep command allows you to pull and vendor requirements for your Go application, avoiding version conflicts and ensuring that your project works correctly.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="97537-125">取得 dep 相依性管理員</span><span class="sxs-lookup"><span data-stu-id="97537-125">Get the dep dependency manager</span></span>](https://github.com/golang/dep)
