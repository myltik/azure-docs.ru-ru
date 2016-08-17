<properties
	pageTitle="О дисках и виртуальных жестких дисках для виртуальных машин Linux | Microsoft Azure"
	description="Информация об основах использования дисков и виртуальных жестких дисков для виртуальных машин Linux в Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# О дисках и виртуальных жестких дисках для виртуальных машин Azure

Как и любой другой компьютер, виртуальные машины в Azure используют диски как место хранения операционной системы, приложений и данных. Все виртуальные машины Azure имеют по крайней мере два диска — диск операционной системы Linux (в случае виртуальной машины Linux) и временный диск. Диск операционной системы создается из образа, а диск операционной системы и образ в действительности являются виртуальными жесткими дисками (VHD), расположенными в учетной записи хранения Azure. Кроме того, виртуальные машины могут иметь один или несколько дисков данных, которые также хранятся на виртуальных жестких дисках. Также доступна версия этой статьи для [виртуальных машин Windows](virtual-machines-windows-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Устранение неполадок
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## Дальнейшие действия

-  [Подключите диск](virtual-machines-linux-attach-disk-portal.md), чтобы увеличить емкость хранилища для виртуальной машины.
-  [Настройте программный RAID](virtual-machines-linux-configure-raid.md), чтобы обеспечить избыточность.
-  [Запишите образ виртуальной машины Linux](virtual-machines-linux-classic-capture-image.md), чтобы иметь возможность быстро развернуть дополнительные виртуальные машины.

<!---HONumber=AcomDC_0803_2016-->