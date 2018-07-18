---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 02/14/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: d021dd8ef4744b7c50b296b231bf63481f92411a
ms.sourcegitcommit: 2a3bd491e087a1d0e7d269bed896c029357d62a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988055"
---
[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 與 Go 1.8 版和更新版本相容。 若為使用 [Azure Stack 設定檔](https://docs.microsoft.com/azure/azure-stack/azure-stack-version-profiles)的環境，Go 1.9 版是最低需求。
如果您的系統上沒有 Go 可用，請遵循 [Go 安裝指示](https://golang.org/doc/install)。

您可以透過 `go get` 取得 zure SDK for Go 與其相依性。

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> 務必將 URL 中的 `Azure` 變成大寫。 使用 SDK 時，若未這麼做可能會造成與大小寫相關的匯入問題。 您也需要將您匯入陳述式中的 `Azure` 變成大寫。
