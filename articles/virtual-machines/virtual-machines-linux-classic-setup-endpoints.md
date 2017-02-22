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
ms.sourcegitcommit: 3136b8345d0c851c29a9498089da73c8564549d1
ms.openlocfilehash: 9fe4e1356064d62d1a373ecbabf4f9cf276524f6


---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Как настроить конечные точки в классической виртуальной машине Linux в Azure
Все виртуальные машины Linux, созданные в Azure с помощью классической модели развертывания, могут автоматически взаимодействовать с другими виртуальными машинами в той же облачной службе или виртуальной сети, используя канал частной сети. Однако компьютерам в Интернете или другим виртуальным сетям требуются конечные точки, чтобы направить входящий трафик к виртуальной машине. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

В модели развертывания с помощью **Resource Manager** конечные точки настраиваются с использованием **групп безопасности сети (NSG)**. Дополнительные сведения см. в статье [Открытие портов и конечных точек](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

При создании виртуальной машины Linux на классическом портале Azure конечная точка для Secure Shell (SSH) обычно создается автоматически. При необходимости при создании виртуальной машины или впоследствии можно настроить дополнительные конечные точки.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Вы также можете создать конечную точку виртуальной машины с помощью [интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Выполните команду **azure vm endpoint create** .
* Если вы создали виртуальную машину с помощью модели развертывания Resource Manager, [создайте группы безопасности сети](../virtual-network/virtual-networks-create-nsg-arm-cli.md) с помощью интерфейса командной строки Azure в режиме Resource Manager и управляйте трафиком, поступающим к этой виртуальной машине.




<!--HONumber=Jan17_HO5-->


