<properties
	pageTitle="Разные способы создания виртуальной машины Linux | Microsoft Azure"
	description="В этой статье содержатся различные способы создания виртуальной машины Linux в Azure, а также ссылки на дальнейшие руководства."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Различные способы создания виртуальной машины Linux с помощью Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

Azure поддерживает несколько способов создания виртуальных машин для различных пользователей и целей. В этой статье описаны различия и компоненты, которые можно выбрать при создании виртуальной машины Linux.

Шаблоны Azure Resource Manager позволяют создавать виртуальные машины, а также управлять такими виртуальными машинами и входящими в них ресурсами как одной логической единицей развертывания. Дополнительные сведения о диспетчере ресурсов Azure и управлении ресурсами как одной единицей см. в [статье с обзором](../resource-group-overview.md).

## Выбор средств

### Командная оболочка: Azure CLI 

Воспользуйтесь интерфейсом командной строки Azure (Azure CLI). См. следующие руководства по использованию Azure CLI:

* [Create a Linux VM from the Azure CLI for Dev and Test (Создание виртуальной машины Linux для разработки и тестирования с помощью Azure CLI)](virtual-machines-linux-quick-create-cli.md) 

* [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

### Графический интерфейс пользователя: портал Azure

Графический пользовательский интерфейс [портала Azure](https://portal.azure.com) — это простой способ опробовать виртуальную машину, особенно если вы только начинаете работать с Azure. Для создания виртуальной машины используйте портал Azure:

* [Создание виртуальной машины, работающей под управлением ОС Linux, на портале Azure](virtual-machines-linux-portal-create.md) 

## Операционная система и варианты образов

Выберите образ операционной системы, которую вы хотите запустить. Azure и ее партнеры предлагают множество образов, некоторые из которых содержат приложения и средства. Вы также можете использовать один из собственных образов.

### Образы Azure

Во всех перечисленных выше статьях для создания виртуальной машины и ее настройки для работы в сети, балансировки нагрузки и многих других задач можно легко использовать существующий образ Azure. На портале можно получить доступ к Azure Marketplace с образами от Azure. Аналогичные списки можно получить с помощью командной строки. Например, выполнив в Azure CLI команду `azure vm image list`, вы получите список всех доступных образов, отсортированный по расположению и издателям. См. статью [Просмотр и выбор образов виртуальных машин Linux в Azure с помощью интерфейса командной строки или оболочки PowerShell](virtual-machines-linux-cli-ps-findimage.md).

### Использование своего образа

Используйте образ существующей виртуальной машины Azure. Для этого *запишите* образ такой виртуальной машины или отправьте свой образ, сохраненный на виртуальном жестком диске. Дополнительные сведения см. в следующих статьях:

* [Linux on Azure-Endorsed Distributions (Linux на дистрибутивах, рекомендованных для Azure)](virtual-machines-linux-endorsed-distros.md)

* [Information for Non-Endorsed Distributions (Информация о нерекомендованных дистрибутивах)](virtual-machines-linux-create-upload-generic.md)

* [Как записать образ виртуальной машины Linux для использования в качестве шаблона Resource Manager](virtual-machines-linux-capture-image.md)

## Дальнейшие действия

* Попробуйте выполнить инструкции, приведенные в следующих руководствах по созданию виртуальной машины Linux: с помощью [портала](virtual-machines-linux-portal-create.md), [CLI](virtual-machines-linux-quick-create-cli.md) или [шаблона](virtual-machines-linux-cli-deploy-templates.md) Azure Resource Manager.

* Создав виртуальную машину Linux, вы сможете легко [добавить диск данных](virtual-machines-linux-add-disk.md).

<!---HONumber=AcomDC_0420_2016-->