<properties
   pageTitle="Создание шаблонов с помощью расширений виртуальной машины Windows | Microsoft Azure"
   description="Узнайте о разработке шаблонов Azure Resource Manager с расширениями для виртуальных машин Windows."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Authoring Azure Resource Manager templates with Windows VM extensions

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Выполните следующий командлет в Azure PowerShell:

      Get-AzureVMAvailableExtension


Данный командлет возвращает имя издателя, имя расширения и версию в следующем виде.

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Эти три свойства сопоставляются со значениями publisher, type и typeHandlerVersion в приведенном выше фрагменте шаблона.

>[AZURE.NOTE]Рекомендуется всегда использовать самую последнюю версию расширения для обеспечения максимальной функциональности.

## Определение схемы для параметров конфигурации расширения

Следующий шаг при создании шаблона расширения заключается в определении формата для предоставления параметров конфигурации. Каждое расширение поддерживает собственный набор параметров.

Чтобы ознакомиться с примером конфигурации для расширений Windows, см. [примеры расширений Windows](virtual-machines-windows-extensions-configuration-samples.md).


Ознакомьтесь со следующими разделами, чтобы получить полноценный шаблон с расширениями виртуальной машины.

[Расширение Custom Script на виртуальной машине Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


После разработки шаблон можно развернуть с помощью Azure PowerShell.

<!---HONumber=AcomDC_0601_2016-->