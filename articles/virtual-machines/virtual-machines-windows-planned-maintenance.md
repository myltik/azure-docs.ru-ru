<properties
	pageTitle="Плановое обслуживание виртуальных машин Windows | Microsoft Azure"
	description="Сведения о том, что такое плановое обслуживание Azure и как оно влияет на виртуальные машины Windows, работающие в Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="drewm"/>

# Плановое обслуживание виртуальных машин в Azure


Сведения о том, что такое плановое обслуживание Azure и как оно влияет на доступность виртуальных машин Windows. Также доступна версия этой статьи для [виртуальных машин Linux](virtual-machines-linux-planned-maintenance.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Причины выполнения Azure планового обслуживания

Microsoft Azure периодически выполняет обновления своих систем по всему миру, чтобы повысить надежность, производительность и безопасность инфраструктуры узлов, в которой работают виртуальные машины. Многие из таких обновлений, включая обновления с сохранением памяти, выполняются без какого-либо влияния на виртуальные машины и облачные службы.

Тем не менее, при некоторых обновлениях требуется перезагрузка виртуальных машин для применения обязательных обновлений в инфраструктуре. При применении исправлений в инфраструктуре необходимо завершить работу виртуальных машин, а затем снова запустить их.

Обратите внимание, что на доступность ваших виртуальных машин могут повлиять два типа обслуживания: плановое и внеплановое. На этой странице описывается, как выполняется плановое обслуживание Microsoft Azure. Дополнительные сведения о внеплановом обслуживании см. в разделе [Различия планового и внепланового обслуживания](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0427_2016-->