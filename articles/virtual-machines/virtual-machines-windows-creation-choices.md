<properties
	pageTitle="Разные способы создания виртуальной машины Linux | Microsoft Azure"
	description="Содержит перечисление различных способов создания виртуальной машины Windows с помощью диспетчера ресурсов."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="03/11/2016"
	ms.author="cynthn"/>

# Различные способы создания виртуальной машины Windows с помощью диспетчера ресурсов

Поскольку виртуальные машины подходят для различных пользователей и целей, в Azure их можно создавать несколькими способами. Это означает, что вам потребуется выбрать виртуальную машину и способ ее создания. В настоящей статье рассматриваются эти варианты и приводятся ссылки на инструкции.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


## Портал Azure

Портал Azure — это простой способ опробовать виртуальную машину, особенно если вы только начинаете работать с Azure.

[Создание виртуальной машины под управлением Windows на портале](virtual-machines-windows-hero-tutorial.md)

## Azure PowerShell

Если вы предпочитаете работать в командной строке, можно использовать Azure PowerShell.

- [Создание виртуальной машины Windows с помощью PowerShell](virtual-machines-windows-ps-create.md)
- [Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов](virtual-machines-windows-ps-template.md)

## Шаблон

Для виртуальных машин требуется сочетание ресурсов (таких как группы доступности и учетные записи хранения). Вместо развертывания и управления каждым ресурсом по отдельности можно создать шаблон Azure Resource Manager, который развертывает и подготавливает все ресурсы в ходе одной скоординированной операции.

- [Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов](virtual-machines-windows-ps-template.md)

## Visual Studio

[Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET](virtual-machines-windows-csharp.md)

<!---HONumber=AcomDC_0323_2016-->