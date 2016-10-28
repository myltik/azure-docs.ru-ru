<properties
   pageTitle="Разработка шаблонов с расширениями виртуальной машины Linux | Microsoft Azure"
   description="Узнайте о разработке шаблонов Azure Resource Manager с расширениями для виртуальных машин Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Разработка шаблонов Azure Resource Manager с расширениями виртуальной машины Linux

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

В Azure CLI выполните следующую команду.

      Azure VM extension list

Эта команда возвращает имя издателя, имя расширения и версию в следующем виде.

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Эти три свойства сопоставляются со значениями publisher, type и typeHandlerVersion в приведенном выше фрагменте шаблона.

>[AZURE.NOTE]Рекомендуется всегда использовать самую последнюю версию расширения для обеспечения максимальной функциональности.

## Определение схемы для параметров конфигурации расширения

Следующий шаг при создании шаблона расширения заключается в определении формата для предоставления параметров конфигурации. Каждое расширение поддерживает собственный набор параметров.

Чтобы ознакомиться с примерами конфигураций для расширений Linux, откройте документацию [с примерами расширений Linux](virtual-machines-linux-extensions-configuration-samples.md).

Ознакомьтесь со следующими разделами, чтобы получить полноценный шаблон с расширениями виртуальной машины.

[Расширение пользовательских сценариев на виртуальной машине Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

После разработки шаблон можно развернуть с помощью Azure CLI.

<!---HONumber=AcomDC_0601_2016-->