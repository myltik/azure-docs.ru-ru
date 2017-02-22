---
title: "Настройка конечных точек в классической виртуальной машине Windows | Документация Майкрософт"
description: "Узнайте, как настроить конечные точки для виртуальной машины Windows на классическом портале Azure, чтобы обеспечить обмен данными с виртуальной машиной Windows в Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f022bd4d430fbb94ca9e52f03fd7e5bf69e336ad


---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Настройка конечных точек в классической виртуальной машине Windows в Azure
Все виртуальные машины Windows, созданные в Azure с помощью классической модели развертывания, могут автоматически взаимодействовать с другими виртуальными машинами в той же облачной службе или виртуальной сети, используя канал частной сети. Однако компьютерам в Интернете или другим виртуальным сетям требуются конечные точки, чтобы направить входящий трафик к виртуальной машине. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

В модели развертывания с помощью **Resource Manager** конечные точки настраиваются с использованием **групп безопасности сети (NSG)**. Дополнительные сведения см. в статье [Разрешение внешнего доступа к виртуальной машине с помощью портала Azure](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

При создании виртуальной машины Windows на классическом портале Azure обычно автоматически создаются общие конечные точки, такие как конечные точки для удаленного рабочего стола и удаленного взаимодействия Windows PowerShell. При необходимости при создании виртуальной машины или впоследствии можно настроить дополнительные конечные точки.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Инструкции по использованию командлета Azure PowerShell для настройки конечной точки виртуальной машины см. в разделе [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Инструкции по управлению списком управления доступом к конечной точке с помощью командлета Azure PowerShell см. в разделе [Управление списками управления доступом для конечных точек с помощью PowerShell](../virtual-network/virtual-networks-acl-powershell.md).
* Если вы создали виртуальную машину, используя модель развертывания с помощью Resource Manager, [создайте группы безопасности сети](../virtual-network/virtual-networks-create-nsg-arm-ps.md) с помощью Azure PowerShell и управляйте трафиком к этой виртуальной машине.




<!--HONumber=Dec16_HO1-->


