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
ms.openlocfilehash: 4837572a50ae934e71bfe49d916c01e131bb6d83
ms.sourcegitcommit: f08abf902b48f8173aa6e261084ff2cfc9043305
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
ms.locfileid: "32319691"
---
# <a name="azure-sdk-for-go-samples-for-compute-and-networking"></a><span data-ttu-id="4979c-103">Azure SDK for Go 的計算和網路範例</span><span class="sxs-lookup"><span data-stu-id="4979c-103">Azure SDK for Go samples for compute and networking</span></span>

<span data-ttu-id="4979c-104">下表連結到所選的 Go 原始程式碼範例，可讓您管理 VM、虛擬網路，以及 Azure 中的子網路。</span><span class="sxs-lookup"><span data-stu-id="4979c-104">The following table links to selected samples of Go source code that you can use to manage VMs, virtual networks, and subnets in Azure.</span></span> 

<span data-ttu-id="4979c-105">所有適用於 Azure SDK for Go 的範例都位於 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples)。</span><span class="sxs-lookup"><span data-stu-id="4979c-105">All samples for the Azure SDK for Go are available on [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples).</span></span>

| <span data-ttu-id="4979c-106">Name</span><span class="sxs-lookup"><span data-stu-id="4979c-106">Name</span></span> | <span data-ttu-id="4979c-107">說明</span><span class="sxs-lookup"><span data-stu-id="4979c-107">Description</span></span> |
|------|-------------|
| [<span data-ttu-id="4979c-108">network/network</span><span class="sxs-lookup"><span data-stu-id="4979c-108">network/network</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/network/network.go) | <span data-ttu-id="4979c-109">建立、更新、刪除和查詢包括虛擬網路、子網路和網路安全性群組等等的網路資源。</span><span class="sxs-lookup"><span data-stu-id="4979c-109">Create, update, delete, and query network resources including virtual networks, subnets, and network security groups.</span></span> |
| [<span data-ttu-id="4979c-110">compute/loadbalancer</span><span class="sxs-lookup"><span data-stu-id="4979c-110">compute/loadbalancer</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/loadbalancer.go) | <span data-ttu-id="4979c-111">建立和查詢可用性群組，並建立搭配負載平衡器的 VM。</span><span class="sxs-lookup"><span data-stu-id="4979c-111">Create and query availability groups, and create VMs with a load balancer.</span></span> |
| [<span data-ttu-id="4979c-112">compute/compute</span><span class="sxs-lookup"><span data-stu-id="4979c-112">compute/compute</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/compute.go) | <span data-ttu-id="4979c-113">建立、刪除、更新並更加強管理 VM。</span><span class="sxs-lookup"><span data-stu-id="4979c-113">Create, delete, update, and power-manage VMs.</span></span> <span data-ttu-id="4979c-114">使用資料磁碟和管理 VM 的作業系統磁碟。</span><span class="sxs-lookup"><span data-stu-id="4979c-114">Work with data disks and managing the OS disk of the VM.</span></span> |
