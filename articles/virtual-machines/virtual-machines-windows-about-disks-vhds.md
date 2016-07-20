<properties
	pageTitle="О дисках и виртуальных жестких дисках для виртуальных машин Windows | Microsoft Azure"
	description="Информация об основах использования дисков и виртуальных жестких дисков для виртуальных машин Windows в Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# О дисках и виртуальных жестких дисках для виртуальных машин Azure

Как и любой другой компьютер, виртуальные машины в Azure используют диски как место хранения операционной системы, приложений и данных. Все виртуальные машины Azure имеют по крайней мере два диска — диск операционной системы Windows (в случае виртуальной машины Windows) и временный диск. Диск операционной системы создается из образа, а диск операционной системы и образ в действительности являются виртуальными жесткими дисками (VHD), расположенными в учетной записи хранения Azure. Кроме того, виртуальные машины могут иметь один или несколько дисков данных, которые также хранятся на виртуальных жестких дисках. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Дальнейшие действия
-  [Запишите образ виртуальной машины Windows](virtual-machines-windows-capture-image.md), чтобы иметь возможность масштабировать развертывание виртуальных машин.
-  [Передайте образ виртуальной машины Windows в Azure](virtual-machines-windows-upload-image.md), чтобы использовать его при создании новой виртуальной машины.
-  [Измените букву временного диска Windows](virtual-machines-windows-classic-change-drive-letter.md), чтобы приложение могло использовать диск D для данных.

<!-----HONumber=AcomDC_0622_2016-->
