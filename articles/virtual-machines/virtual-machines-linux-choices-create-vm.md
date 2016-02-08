<properties
	pageTitle="Разные способы создания виртуальной машины Linux | Microsoft Azure"
	description="Содержит различные способы создания виртуальной машины Linux в Azure, а также ссылки на дальнейшие указания."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Различные способы создания виртуальной машины Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure поддерживает несколько способов создания виртуальных машин для различных пользователей и целей. В этой статье описаны различия и компоненты, которые можно выбрать при создании виртуальной машины Linux.

Создавать виртуальные машины, а также управлять такими машинами и входящими в них различными ресурсами как одной логической единицей развертывания можно также с помощью шаблонов диспетчера ресурсов Azure. Инструкции относительно данного подхода прилагаются (при наличии). Дополнительные сведения о диспетчере ресурсов Azure и управлении ресурсами как одной единицей см. в [статье с обзором][].

## Выбор средств

### Графический пользовательский интерфейс: классический портал Azure или портал Azure

Графический пользовательский интерфейс портала — это простой способ опробовать виртуальную машину, особенно если вы только начали работать с Azure. Для создания виртуальной машины используется [классический портал Azure](https://manage.windowsazure.com) или [портал Azure](https://portal.azure.com). Для получения общих инструкций изучите статью [Создание настраиваемой виртуальной машины][] и выберите любой образ Linux из **коллекции**. Обратите внимание, что [классический портал Azure](https://manage.windowsazure.com) создает виртуальные машины только с использованием классической модели развертывания.

### Командная оболочка: CLI Azure или Azure PowerShell

Если вы предпочитаете работать в командной оболочке, выберите интерфейс командной строки (CLI) Azure для пользователей компьютеров под управлением Mac, Linux и Windows или оболочку Azure PowerShell, которая содержит командлеты Windows PowerShell для Azure и пользовательской консоли.

Инструкции по созданию виртуальных машин с использованием модели развертывания диспетчера ресурсов и интерфейса командной строки Azure см. в статье [Создание виртуальной машины Linux][]. Для просмотра инструкции по работе с Azure PowerShell и шаблонами используйте вкладки и селекторы статей.

При использовании классической модели развертывания см. статьи [Создание настраиваемой виртуальной машины под управлением Linux с помощью Azure CLI](virtual-machines-linux-create-custom.md) и [Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Linux][].


### Среда разработки: Visual Studio

Visual Studio также поддерживает создание виртуальных машин Azure. При использовании классической модели развертывания прочтите статью [Создание виртуальной машины для веб-сайта с помощью Visual Studio][]. При использовании модели развертывания диспетчера ресурсов прочтите статью [Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET][].


## Операционная система и варианты образов

Выберите образ операционной системы, которую вы хотите запустить. Azure и ее партнеры предлагают множество образов, некоторые из которых содержат приложения и средства. Вы также можете использовать один из собственных образов.


### Образы Azure

Во всех перечисленных выше статьях для создания виртуальной машины и ее настройки для работы в сети, балансировки нагрузки и многих других задач можно легко использовать существующий образ Azure. Порталы предоставляют коллекцию или список образов от Azure. Аналогичные списки можно получить с помощью командной строки. Например, выполнив в Azure CLI команду `azure vm image list`, вы получите список всех доступных образов, отсортированный по местонахождению и издателям.


### Использование собственного образа

Используйте образ существующей виртуальной машины Azure. Для этого *запишите* ее или передайте собственный образ, сохраненный на виртуальном жестком диске. При использовании классической модели развертывания см. статьи [Запись образа виртуальной машины Linux с помощью CLI для использования в качестве шаблона][] и [Создание и передача виртуального жесткого диска с операционной системой Linux][]. При использовании модели развертывания диспетчера ресурсов см. статью [Запись виртуальной машины Linux как шаблона диспетчера ресурсов](virtual-machines-linux-capture-image-resource-manager.md).

## Дальнейшие действия

[Вход на виртуальную машину][]

[Присоединение диска данных][]

## Дополнительные ресурсы

[Тестовая среда с базовой конфигурацией][]

[Гибридные облачные тестовые среды Azure][]

[Эквивалентные команды диспетчера ресурсов и управления службами для операций с виртуальными машинами в Azure CLI для Mac, Linux и Windows][]

<!-- LINKS -->
[статье с обзором]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Эквивалентные команды диспетчера ресурсов и управления службами для операций с виртуальными машинами в Azure CLI для Mac, Linux и Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Linux]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[Запись образа виртуальной машины Linux с помощью CLI для использования в качестве шаблона]: virtual-machines-linux-capture-image.md

[Создание и передача виртуального жесткого диска с операционной системой Linux]: virtual-machines-linux-create-upload-vhd.md

[Создание виртуальной машины для веб-сайта с помощью Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Развертывание ресурсов Azure с помощью библиотек компонентов Compute, Network и Storage для .NET]: virtual-machines-arm-deployment.md

[Вход на виртуальную машину]: virtual-machines-linux-how-to-log-on.md

[Присоединение диска данных]: virtual-machines-linux-how-to-attach-disk.md

[Тестовая среда с базовой конфигурацией]: virtual-machines-base-configuration-test-environment.md
[Гибридные облачные тестовые среды Azure]: virtual-machines-hybrid-cloud-test-environments.md

[Создание виртуальной машины Linux]: virtual-machines-linux-tutorial.md
[Создание настраиваемой виртуальной машины]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->