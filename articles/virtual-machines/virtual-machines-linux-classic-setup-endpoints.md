---
title: Настройка конечных точек в классической виртуальной машине Linux | Microsoft Docs
description: Узнайте, как настроить конечные точки для виртуальной машины Linux на классическом портале Azure, чтобы обеспечить обмен данными с виртуальной машиной Linux в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn

---
# Как настроить конечные точки в классической виртуальной машине Linux в Azure
Все виртуальные машины Linux, созданные в Azure с помощью классической модели развертывания, могут автоматически взаимодействовать с другими виртуальными машинами в той же облачной службе или виртуальной сети, используя канал частной сети. Однако компьютерам в Интернете или другим виртуальным сетям требуются конечные точки, чтобы направить входящий трафик к виртуальной машине. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-classic-setup-endpoints.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

В модели развертывания с помощью **Resource Manager** конечные точки настраиваются с использованием **групп безопасности сети**. Дополнительные сведения см. в статье [Открытие портов и конечных точек](virtual-machines-linux-nsg-quickstart.md).

При создании виртуальной машины Linux на классическом портале Azure конечная точка для Secure Shell (SSH) обычно создается автоматически. При необходимости при создании виртуальной машины или впоследствии можно настроить дополнительные конечные точки.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Дальнейшие действия
* Вы также можете создать конечную точку виртуальной машины с помощью [интерфейса командной строки Azure](../virtual-machines-command-line-tools.md). Выполните команду **azure vm endpoint create**.
* Если вы создали виртуальную машину с помощью модели развертывания Resource Manager, [создайте группы безопасности сети](../virtual-network/virtual-networks-create-nsg-arm-cli.md) с помощью интерфейса командной строки Azure в режиме Resource Manager и управляйте трафиком, поступающим к этой виртуальной машине.

<!---HONumber=AcomDC_0713_2016-->