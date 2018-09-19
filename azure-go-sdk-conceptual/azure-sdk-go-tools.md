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
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>使用 Azure SDK for Go 的開發人員所適用的工具

為了能有效率地撰寫 Go 程式碼並且讓它與 Azure 服務完美配合運作，以下有一些建議的工具。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 提供的命令列介面可建立及設定您訂用帳戶中的 Azure 資源。 CLI 可協助您開始快速建置一般和共用 Azure 資源，讓您可以專注於更複雜的服務使用方式。 CLI 具有查詢和篩選功能，因此您可以直接將輸出輸送到您最愛的命令列工具。 CLI 可以 Docker 映像形式，或透過 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 安裝在您的本機系統上。

> [!div class="nextstepaction"]
> [安裝 Azure CLI](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code 是輕量型編輯器，可提供 Go 支援。 此擴充功能提供自動完成、`impl` 範本、重構和偵錯等功能。 Visual Studio Code 也支援從編輯器內存取原始檔控制，並提供可與 Azure 服務搭配使用的擴充功能。

* [安裝 Visual Studio Code](https://code.visualstudio.com/Download)
* [取得 Visual Studio Code Go 擴充功能](https://code.visualstudio.com/docs/languages/go)
* [取得 Visual Studio Code Azure Tools 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>CI/CD 搭配 Azure DevOps Project

Azure DevOps Project 管線可讓您為 Go 應用程式設定持續整合系統。 只要有 Git 存放庫，您就可以直接在 Azure 上進行部署和測試。

> [!div class="nextstepaction"]
> [了解如何利用 Azure DevOps Projects 建立 CI/CD 管道](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>使用 dep 進行相依性管理

Azure SDK for Go 會使用 dep 進行相依性管理。 dep 命令可讓您提取 Go 應用程式的需求，並將其放到 vendor 目錄下，以避免發生版本衝突，並確保您的專案運作正常。

> [!div class="nextstepaction"]
> [取得 dep 相依性管理員](https://github.com/golang/dep)
