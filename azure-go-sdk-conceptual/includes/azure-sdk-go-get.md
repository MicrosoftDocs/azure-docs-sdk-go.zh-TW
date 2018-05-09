---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 02/14/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: ddd58efdbc0c2d3ded068a9bebf2466db702566f
ms.sourcegitcommit: f08abf902b48f8173aa6e261084ff2cfc9043305
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
<span data-ttu-id="43af3-101">[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 與 Go 1.8 版和更新版本相容。</span><span class="sxs-lookup"><span data-stu-id="43af3-101">The [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) is compatible with Go versions 1.8 and later.</span></span> <span data-ttu-id="43af3-102">若為使用 [Azure Stack 設定檔](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles)的環境，Go 1.9 版是最低需求。</span><span class="sxs-lookup"><span data-stu-id="43af3-102">For environments using [Azure Stack Profiles](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles), Go version 1.9 is the minimum requirement.</span></span>
<span data-ttu-id="43af3-103">如果您的系統上沒有 Go 可用，請遵循 [Go 安裝指示](https://golang.org/doc/install)。</span><span class="sxs-lookup"><span data-stu-id="43af3-103">If you do not have Go available on your system, follow [the Go installation instructions](https://golang.org/doc/install).</span></span>

<span data-ttu-id="43af3-104">您可以透過 `go get` 取得 zure SDK for Go 與其相依性。</span><span class="sxs-lookup"><span data-stu-id="43af3-104">You can obtain the Azure SDK for Go and its dependencies via `go get`.</span></span>

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> <span data-ttu-id="43af3-105">務必將 URL 中的 `Azure` 變成大寫。</span><span class="sxs-lookup"><span data-stu-id="43af3-105">Make sure that you capitalize `Azure` in the URL.</span></span> <span data-ttu-id="43af3-106">使用 SDK 時，若未這麼做可能會造成與大小寫相關的匯入問題。</span><span class="sxs-lookup"><span data-stu-id="43af3-106">Doing otherwise can cause case-related import problems when working with the SDK.</span></span> <span data-ttu-id="43af3-107">您也需要將您匯入陳述式中的 `Azure` 變成大寫。</span><span class="sxs-lookup"><span data-stu-id="43af3-107">You also need to capitalize `Azure` in your import statements.</span></span>

