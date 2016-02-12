<properties
	pageTitle="Разные способы создания виртуальной машины Linux | Microsoft Azure"
	description="Содержит перечисление различных способов создания виртуальной машины Windows с помощью диспетчера ресурсов."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# Различные способы создания виртуальной машины Windows с помощью диспетчера ресурсов

Поскольку виртуальные машины подходят для различных пользователей и целей, в Azure их можно создавать несколькими способами. Это означает, что вам потребуется выбрать виртуальную машину и способ ее создания. В настоящей статье рассматриваются эти варианты и приводятся ссылки на инструкции.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

Недавно мы представили шаблоны диспетчера ресурсов Azure, которые позволяют создать виртуальную машину, а также управлять этой машиной и входящими в нее различными ресурсами как одной логической единицей развертывания. Инструкции относительно данного подхода приведены ниже (при наличии). Дополнительные сведения о диспетчере ресурсов Azure и управлении ресурсами как одной единицей см. в статье [Обзор][].

## Выбор средств

### Графический интерфейс пользователя: портал Azure

Графический пользовательский интерфейс классического портала Azure — это простой способ опробовать виртуальную машину, особенно если вы только начали работать с Azure. Создайте виртуальную машину с помощью портала Azure.

[Создание виртуальной машины под управлением Windows][]

### Командная оболочка: CLI Azure или Azure PowerShell

Если вы предпочитаете работать в командной оболочке, выберите интерфейс командной строки (CLI) Azure для пользователей компьютеров под управлением Mac и Linux или оболочку Azure PowerShell, которая содержит командлеты для Azure и пользовательской консоли.

Если вы хотите использовать Azure CLI, см. следующие ресурсы:

- [Эквивалентные команды диспетчера ресурсов и управления службами для операций с виртуальными машинами в Azure CLI для Mac, Linux и Windows.][]
- [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure][]

Если вы хотите использовать Azure PowerShell, см. следующие ресурсы:

- [Создание виртуальной машины Windows с помощью диспетчера ресурсов и PowerShell][]
- [Создание и предварительная настройка виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell][]
- [Развертывание виртуальных машин и управление ими с использованием шаблонов диспетчера ресурсов Azure и PowerShell][]
- [Создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и PowerShell][]

### Среда разработки: Visual Studio

[Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET][]

## Операционная система и варианты образов

Выберите образ операционной системы, которую вы хотите запустить. Azure и ее партнеры предлагают множество образов, некоторые из которых содержат приложения и средства. Вы также можете использовать один из собственных образов. Найдите образ платформы, которая требуется для вашего приложения, с помощью поиска информации в следующем разделе: [Выполните обзор и выберите образы виртуальных машин Azure с помощью Windows PowerShell и Azure CLI][].

<!-- LINKS -->
[Обзор]: ../resource-group-overview.md

[Создание виртуальной машины под управлением Windows]: virtual-machines-windows-tutorial.md

[Эквивалентные команды диспетчера ресурсов и управления службами для операций с виртуальными машинами в Azure CLI для Mac, Linux и Windows.]: xplat-cli-azure-manage-vm-asm-arm.md
[Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Создание и предварительная настройка виртуальной машины Windows с помощью диспетчера ресурсов и Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Развертывание виртуальных машин и управление ими с использованием шаблонов диспетчера ресурсов Azure и PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Создание виртуальной машины Windows с помощью диспетчера ресурсов и PowerShell]: virtual-machines-create-windows-powershell-resource-manager.md
[Создание виртуальной машины Windows с помощью шаблона диспетчера ресурсов и PowerShell]: virtual-machines-create-windows-powershell-resource-manager-template.md


[Выполните обзор и выберите образы виртуальных машин Azure с помощью Windows PowerShell и Azure CLI]: resource-groups-vm-searching.md
[Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=AcomDC_0204_2016-->