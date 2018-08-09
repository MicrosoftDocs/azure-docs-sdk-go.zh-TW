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
# <a name="azure-sdk-for-go-samples-for-compute-and-networking"></a><span data-ttu-id="4e714-103">Azure SDK for Go 的計算和網路範例</span><span class="sxs-lookup"><span data-stu-id="4e714-103">Azure SDK for Go samples for compute and networking</span></span>

<span data-ttu-id="4e714-104">下表連結到所選的 Go 原始程式碼範例，可讓您管理 VM、虛擬網路，以及 Azure 中的子網路。</span><span class="sxs-lookup"><span data-stu-id="4e714-104">The following table links to selected samples of Go source code that you can use to manage VMs, virtual networks, and subnets in Azure.</span></span> 

<span data-ttu-id="4e714-105">所有適用於 Azure SDK for Go 的範例都位於 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples)。</span><span class="sxs-lookup"><span data-stu-id="4e714-105">All samples for the Azure SDK for Go are available on [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples).</span></span>

| <span data-ttu-id="4e714-106">Name</span><span class="sxs-lookup"><span data-stu-id="4e714-106">Name</span></span> | <span data-ttu-id="4e714-107">說明</span><span class="sxs-lookup"><span data-stu-id="4e714-107">Description</span></span> |
|------|-------------|
| [<span data-ttu-id="4e714-108">network/network</span><span class="sxs-lookup"><span data-stu-id="4e714-108">network/network</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/network/network.go) | <span data-ttu-id="4e714-109">建立、更新、刪除和查詢包括虛擬網路、子網路和網路安全性群組等等的網路資源。</span><span class="sxs-lookup"><span data-stu-id="4e714-109">Create, update, delete, and query network resources including virtual networks, subnets, and network security groups.</span></span> |
| [<span data-ttu-id="4e714-110">compute/vm_disk</span><span class="sxs-lookup"><span data-stu-id="4e714-110">compute/vm_disk</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm_disk.go) | <span data-ttu-id="4e714-111">建立、連結、中斷連結、更新及加密 VM 資料磁碟。</span><span class="sxs-lookup"><span data-stu-id="4e714-111">Create, attach, detatch, update, and encrypt data disks for a VM.</span></span> |
| [<span data-ttu-id="4e714-112">compute/vm</span><span class="sxs-lookup"><span data-stu-id="4e714-112">compute/vm</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go) | <span data-ttu-id="4e714-113">建立、更新、停用及管理 VM。</span><span class="sxs-lookup"><span data-stu-id="4e714-113">Create, update, deactivate, and manage VMs.</span></span> |
| [<span data-ttu-id="4e714-114">compute/vm_with_availabilityset</span><span class="sxs-lookup"><span data-stu-id="4e714-114">compute/vm_with_availabilityset</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm_with_availabilityset.go) | <span data-ttu-id="4e714-115">建立可用性設定組並上傳適用於 VM 的負載平衡器。</span><span class="sxs-lookup"><span data-stu-id="4e714-115">Create availability sets and load balancers for VMs.</span></span> |
| [<span data-ttu-id="4e714-116">compute/vm_with_identity</span><span class="sxs-lookup"><span data-stu-id="4e714-116">compute/vm_with_identity</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm_with_identity.go) | <span data-ttu-id="4e714-117">建立和管理適用於 VM 的受控服務識別 (MSI)。</span><span class="sxs-lookup"><span data-stu-id="4e714-117">Create and manage Managed Service Identities (MSIs) for VMs.</span></span> |
