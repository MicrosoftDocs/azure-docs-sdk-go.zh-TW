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
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>使用 Azure SDK for Go 的開發人員所適用的工具

為了能有效率地撰寫 Go 程式碼並且讓它與 Azure 服務完美配合運作，以下有一些建議的工具。

## <a name="azure-cli-20"></a>Azure CLI 2.0

Azure 2.0 CLI 提供的命令列介面可建立及設定您訂用帳戶中的 Azure 資源。 CLI 可協助您開始快速建置一般和共用 Azure 資源，讓您可以專注於更複雜的服務使用方式。 CLI 具有查詢和篩選功能，因此您可以直接將輸出輸送到您最愛的命令列工具。 CLI 可以 Docker 映像形式，或透過 [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) 安裝在您的本機系統上。

> [!div class="nextstepaction"]
> [安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 是一個輕量級編輯器，可透過擴充功能完整支援 Go 語言。 這些擴充功能包含支援自動完成、`impl` 範本、重構和偵錯等功能。 Visual Studio Code 還為一般開發人員工具提供許多擴充功能 (例如原始檔控制)，甚至提供可與 Azure 服務進行直接互動的擴充功能。 Microsoft 會維護包含這些 Azure 擴充功能的一項正式中繼擴充功能，包括 Azure CLI 的互動式介面。

* [安裝 Visual Studio Code](https://code.visualstudio.com/Download)
* [取得 Visual Studio Code Go 擴充功能](https://code.visualstudio.com/docs/languages/go)
* [取得 Azure Tools 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="dependency-management-with-dep"></a>使用 dep 進行相依性管理

因為還沒有正式的解決方案，所以有許多方法可使用 Go 來管理套件相依性並進行 Vendoring 處理。 執行此管理的建議方法是使用 `dep` 相依性管理員。 Azure SDK for Go 會使用 dep 進行其 Vendoring 處理，並保證可使用 dep 正確取得其他任何專案的相依性。

> [!div class="nextstepaction"]
> [取得 dep 相依性管理員](https://github.com/tools/godep)

## <a name="telemetry-with-application-insights"></a>使用 Application Insights 進行遙測

[Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) 是一項分析產品，可讓您輕鬆地從應用程式中收集遙測資訊，以及與 Azure 生態系統、Visual Studio Team Services 和 GitHub 整合。 該產品使用於許多應用程式，而 Microsoft 提供 Go SDK 來搭配 Application Insights 運作。

> [!div class="nextstepaction"]
> [取得 Application Insights for Go SDK](https://github.com/Microsoft/ApplicationInsights-Go) 