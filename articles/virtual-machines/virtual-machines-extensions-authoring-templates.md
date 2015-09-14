<properties
   pageTitle="Создание шаблонов с помощью расширений виртуальных машин Azure | Microsoft Azure"
	description="Дополнительные сведения о создании шаблонов с помощью расширений"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="kundanap"/>

# Создание шаблонов диспетчера ресурсов Azure с расширениями виртуальных машин

## Общие сведения о шаблонах диспетчере ресурсов Azure

Шаблон диспетчера ресурсов Azure позволяет декларативно задать IaaS-инфраструктуру Azure на языке JSON, установив зависимости между ресурсами. Более подробно шаблоны диспетчера ресурсов Azure рассматриваются в следующих статьях.

<a href="https://azure.microsoft.com/ru-RU/documentation/articles/resource-group-overview/" target="_blank">Общие сведения о группе ресурсов</a> <br/> <a href="https://azure.microsoft.com/ru-RU/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Развертывание шаблонов с помощью интерфейса командной строки Azure</a> <br/> <a href="https://azure.microsoft.com/ru-RU/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Развертывание шаблонов с помощью Azure Powershell</a>

## Фрагмент шаблона образца для расширений виртуальной машины
Фрагмент шаблона для развертывания расширений выглядит следующим образом:

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

## Идентификация значений "publisher", "type" и "typeHandlerVersion" для любого расширения

Расширения виртуальных машин Azure публикуются корпорацией Майкрософт и являются доверенными для сторонних издателей, при этом каждое расширение однозначно идентифицируется по значениям "publisher", "type" и "typeHandlerVersion". Эти сведения можно определить следующим образом:

Выполните следующий командлет в Azure PowerShell:

      Get-AzureVMAvailableExtension

Выполните следующий командлет в интерфейсе командной строки Azure:

      Azure VM Extension list

Данный командлет возвращает имя издателя, имя расширения и версию в следующем виде:

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Эти три свойства сопоставляются со значениями "publisher", "type" и "typeHandlerVersion" в приведенном выше фрагменте шаблона. Примечание. Рекомендуется всегда использовать самую последнюю версию расширения для обеспечения максимальной функциональности.

## Определение схемы для параметров конфигурации расширения

Следующий шаг при создании шаблона расширения заключается в определении формата для предоставления параметров конфигурации. Каждое расширение поддерживает собственный набор параметров.

Чтобы ознакомиться с примером конфигурации для расширений Windows, откройте раздел [с примерами расширений Windows](virtual-machines-extensions-configuration-samples-windows.md).

Чтобы ознакомиться с примером конфигурации для расширений Linux, откройте раздел [с примерами расширений Linux](virtual-machines-extensions-configuration-samples-linux.md).

Ознакомьтесь со следующими разделами, посвященными шаблонам виртуальных машин, для получения полноценного шаблона с расширениями виртуальных машин.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Расширение пользовательского сценария на виртуальной машине Linux</a> </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Расширение пользовательского сценария на виртуальной машине Windows</a>

Развернуть созданный шаблон можно с помощью интерфейса командной строки Azure или Azure Powershell.

<!---HONumber=September15_HO1-->