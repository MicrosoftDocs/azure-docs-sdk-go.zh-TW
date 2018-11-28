---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: 5df14f939efdd0550b49261c88c8dc6518ada459
ms.sourcegitcommit: 887b15afcdeaf926a5f3d21b64e4045167fd062c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2018
ms.locfileid: "52293483"
---
[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 與 Go 1.8 版和更新版本相容。 若為使用 [Azure Stack 設定檔](/azure/azure-stack/user/azure-stack-version-profiles-go)的環境，Go 1.9 版是最低需求。
如果您需要安裝 Go，請遵循 [Go 安裝指示](https://golang.org/doc/install)。

您可以透過 `go get` 下載 Azure SDK for Go 與其相依性。

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> 務必將 URL 中的 `Azure` 變成大寫。 使用 SDK 時，若未這麼做可能會造成與大小寫相關的匯入問題。 您也需要將您匯入陳述式中的 `Azure` 變成大寫。
