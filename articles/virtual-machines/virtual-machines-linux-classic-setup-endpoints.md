---
title: "Настройка конечных точек на классической виртуальной машине Linux | Документация Майкрософт"
description: "Узнайте, как настроить конечные точки для виртуальной машины Linux на классическом портале Azure, чтобы обеспечить обмен данными с виртуальной машиной Linux в Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 372fc76485720f34e7c5b85edf83a01aa5263022


---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Как настроить конечные точки в классической виртуальной машине Linux в Azure
Все виртуальные машины Linux, созданные в Azure с помощью классической модели развертывания, могут автоматически взаимодействовать с другими виртуальными машинами в той же облачной службе или виртуальной сети, используя канал частной сети. Однако компьютерам в Интернете или другим виртуальным сетям требуются конечные точки, чтобы направить входящий трафик к виртуальной машине. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

В модели развертывания с помощью **Resource Manager** конечные точки настраиваются с использованием **групп безопасности сети (NSG)**. Дополнительные сведения см. в статье [Открытие портов и конечных точек](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

При создании виртуальной машины Linux на классическом портале Azure конечная точка для Secure Shell (SSH) обычно создается автоматически. При необходимости при создании виртуальной машины или впоследствии можно настроить дополнительные конечные точки.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Вы также можете создать конечную точку виртуальной машины с помощью [интерфейса командной строки Azure](../virtual-machines-command-line-tools.md). Выполните команду **azure vm endpoint create** .
* Если вы создали виртуальную машину с помощью модели развертывания Resource Manager, [создайте группы безопасности сети](../virtual-network/virtual-networks-create-nsg-arm-cli.md) с помощью интерфейса командной строки Azure в режиме Resource Manager и управляйте трафиком, поступающим к этой виртуальной машине.




<!--HONumber=Nov16_HO3-->


