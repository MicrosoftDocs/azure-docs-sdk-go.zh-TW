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
[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 與 Go 1.8 版和更新版本相容。 若為使用 [Azure Stack 設定檔](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles)的環境，Go 1.9 版是最低需求。
如果您的系統上沒有 Go 可用，請遵循 [Go 安裝指示](https://golang.org/doc/install)。

您可以透過 `go get` 取得 zure SDK for Go 與其相依性。

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> 務必將 URL 中的 `Azure` 變成大寫。 使用 SDK 時，若未這麼做可能會造成與大小寫相關的匯入問題。 您也需要將您匯入陳述式中的 `Azure` 變成大寫。

