<properties
	pageTitle="Настройка конечных точек в классической виртуальной машине Linux | Microsoft Azure"
	description="Узнайте, как настроить конечные точки на классическом портале Azure, чтобы обеспечить обмен данными с виртуальной машиной Linux в Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Настройка конечных точек в классической виртуальной машине Linux в Azure

Все виртуальные машины Linux, созданные в Azure с помощью классической модели развертывания, могут автоматически взаимодействовать с другими виртуальными машинами в той же облачной службе или виртуальной сети, используя канал частной сети. Однако компьютерам в Интернете или другим виртуальным сетям требуются конечные точки, чтобы направить входящий трафик к виртуальной машине. Сведения о конечных точках в виртуальных машинах Windows см. в статье [Настройка конечных точек в классической виртуальной машине Windows в Azure](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.

При создании виртуальной машины Linux на классическом портале Azure общие конечные точки, такие как конечные точки для Secure Shell (SSH), обычно создаются автоматически. При необходимости при создании виртуальной машины или впоследствии можно настроить дополнительные конечные точки.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Дальнейшие действия

* Вы также можете создать конечную точку виртуальной машины с помощью интерфейса командной строки Azure в [режиме управления службами](../virtual-machines-command-line-tools.md). Выполните команду **azure vm endpoint create**.

<!---HONumber=AcomDC_0330_2016-->