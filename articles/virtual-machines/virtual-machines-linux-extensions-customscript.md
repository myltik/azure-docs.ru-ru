<properties
   pageTitle="Применение пользовательских сценариев на виртуальных машинах Linux с помощью шаблонов | Microsoft Azure"
   description="Автоматизируйте задачи настройки виртуальных машин Linux с помощью расширения пользовательских сценариев и шаблонов Resource Manager."
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

# Использование расширения пользовательских сценариев для виртуальных машин Linux с шаблонами Azure Resource Manager

В этой статье описывается создание шаблонов Azure Resource Manager с помощью расширения пользовательских сценариев для начальной загрузки рабочих нагрузок на виртуальную машину Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Пример шаблона для виртуальной машины Linux

В разделе Resource шаблона определите следующий ресурс расширения:

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

В приведенном выше примере замените URL-адрес и имя файла собственными значениями.

После разработки шаблон можно развернуть с помощью Azure CLI.

Полный примеры настройки приложений на виртуальной машине с помощью расширения пользовательских сценариев вы найдете ниже.

* [Расширение пользовательских сценариев на виртуальной машине Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0420_2016-->