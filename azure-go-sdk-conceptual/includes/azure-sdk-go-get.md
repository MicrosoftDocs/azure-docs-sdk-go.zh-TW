---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: 5df14f939efdd0550b49261c88c8dc6518ada459
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059242"
---
<span data-ttu-id="8ec77-101">[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 與 Go 1.8 版和更新版本相容。</span><span class="sxs-lookup"><span data-stu-id="8ec77-101">The [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) is compatible with Go versions 1.8 and higher.</span></span> <span data-ttu-id="8ec77-102">若為使用 [Azure Stack 設定檔](/azure/azure-stack/user/azure-stack-version-profiles-go)的環境，Go 1.9 版是最低需求。</span><span class="sxs-lookup"><span data-stu-id="8ec77-102">For environments using [Azure Stack Profiles](/azure/azure-stack/user/azure-stack-version-profiles-go), Go version 1.9 is the minimum requirement.</span></span>
<span data-ttu-id="8ec77-103">如果您需要安裝 Go，請遵循 [Go 安裝指示](https://golang.org/doc/install)。</span><span class="sxs-lookup"><span data-stu-id="8ec77-103">If you need to install Go, follow [the Go installation instructions](https://golang.org/doc/install).</span></span>

<span data-ttu-id="8ec77-104">您可以透過 `go get` 下載 Azure SDK for Go 與其相依性。</span><span class="sxs-lookup"><span data-stu-id="8ec77-104">You can download the Azure SDK for Go and its dependencies via `go get`.</span></span>

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> <span data-ttu-id="8ec77-105">務必將 URL 中的 `Azure` 變成大寫。</span><span class="sxs-lookup"><span data-stu-id="8ec77-105">Make sure that you capitalize `Azure` in the URL.</span></span> <span data-ttu-id="8ec77-106">使用 SDK 時，若未這麼做可能會造成與大小寫相關的匯入問題。</span><span class="sxs-lookup"><span data-stu-id="8ec77-106">Doing otherwise can cause case-related import problems when working with the SDK.</span></span> <span data-ttu-id="8ec77-107">您也需要將您匯入陳述式中的 `Azure` 變成大寫。</span><span class="sxs-lookup"><span data-stu-id="8ec77-107">You also need to capitalize `Azure` in your import statements.</span></span>
