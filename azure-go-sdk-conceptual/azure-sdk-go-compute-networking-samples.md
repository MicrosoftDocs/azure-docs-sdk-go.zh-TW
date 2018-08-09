---
title: Azure SDK for Go 的計算和網路範例
description: 精選數個範例，供您與 Azure SDK for Go 中的 VM 和虛擬機器等計算資源搭配使用。
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/21/2018
ms.topic: sample
ms.prod: azure
ms.technology: azure-sdk-go
ms.service: virtual-machines
ms.devlang: go
ms.openlocfilehash: 3b31716ee42c638bab4a6dd99b9eb0d7c07e51a4
ms.sourcegitcommit: 0f581979216f7c9d4913681a6d9f6fe09af26e43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39475784"
---
# <a name="azure-sdk-for-go-samples-for-compute-and-networking"></a>Azure SDK for Go 的計算和網路範例

下表連結到所選的 Go 原始程式碼範例，可讓您管理 VM、虛擬網路，以及 Azure 中的子網路。 

所有適用於 Azure SDK for Go 的範例都位於 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples)。

| Name | 說明 |
|------|-------------|
| [network/network](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/network/network.go) | 建立、更新、刪除和查詢包括虛擬網路、子網路和網路安全性群組等等的網路資源。 |
| [compute/vm_disk](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm_disk.go) | 建立、連結、中斷連結、更新及加密 VM 資料磁碟。 |
| [compute/vm](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go) | 建立、更新、停用及管理 VM。 |
| [compute/vm_with_availabilityset](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm_with_availabilityset.go) | 建立可用性設定組並上傳適用於 VM 的負載平衡器。 |
| [compute/vm_with_identity](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm_with_identity.go) | 建立和管理適用於 VM 的受控服務識別 (MSI)。 |
