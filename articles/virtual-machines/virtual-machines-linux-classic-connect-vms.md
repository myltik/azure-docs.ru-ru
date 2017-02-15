---
title: "Подключение виртуальных машин Linux в облачной службе | Документация Майкрософт"
description: "Подключение виртуальных машин Linux, созданных с помощью классической модели развертывания, к облачной службе Azure или виртуальной сети."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 2fd23055-6b34-4ef0-88a8-fc19e32fb3c9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 233e0138122d4c3376957cfb2808c7f274be73da


---
# <a name="connect-linux-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Подключение виртуальных машин Linux, созданных с помощью классической модели развертывания, к виртуальной сети или облачной службе.
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Виртуальные машины Linux, созданные по классической модели развертывания, всегда размещаются в облачной службе. Облачная служба выступает в качестве контейнера и предоставляет уникальное общедоступное DNS-имя, общедоступный IP-адрес и набор конечных точек для доступа к виртуальной машине через Интернет. Облачная служба может находиться в виртуальной сети, но это не обязательно. Вы также можете [подключить виртуальные машины Windows к виртуальной сети или облачной службе](virtual-machines-windows-classic-connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Если облачная служба не находится в виртуальной сети, она называется *автономной* . Виртуальные машины в изолированной облачной службе могут взаимодействовать с другими виртуальными машинами только с помощью общедоступных DNS-имен других виртуальных машин, при этом трафик проходит через Интернет. Если облачная служба находится в виртуальной сети, виртуальные машины в этой облачной службе могут взаимодействовать со всеми остальными виртуальными машинами в виртуальной сети без отправки трафика через Интернет.

Если разместить виртуальные машины в одной автономной облачной службе, можно использовать балансировку нагрузки и группы доступности. Дополнительные сведения см. в разделах [Балансировка нагрузки виртуальных машин](virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и [Управление доступностью виртуальных машин](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Тем не менее, невозможно упорядочить виртуальные машины в подсетях или подключить автономную облачную службу к локальной сети. Ниже приведен пример:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Дальнейшие действия
После создания виртуальной машины рекомендуется [добавить диск данных](virtual-machines-linux-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) , чтобы ваши службы и рабочие нагрузки имели расположение для хранения данных. 




<!--HONumber=Nov16_HO3-->


