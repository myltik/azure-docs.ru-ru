<properties
   pageTitle="Применение пользовательских сценариев на виртуальных машинах Windows с помощью шаблонов | Microsoft Azure"
   description="Автоматизируйте задачи настройки виртуальных машин Windows с помощью расширения пользовательских сценариев и шаблонов Resource Manager."
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

# Использование расширения пользовательских сценариев для виртуальных машин Windows с шаблонами Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Пример шаблона для виртуальной машины Windows

В разделе Resource шаблона определите следующий ресурс:

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

В приведенном выше примере замените URL-адрес и имя файла собственными значениями.

После разработки шаблон можно развернуть с помощью Azure PowerShell.

Полные примеры настройки приложений на виртуальной машине с помощью расширения пользовательских сценариев вы найдете в следующем примере.

* [Расширение пользовательских сценариев на виртуальной машине Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0420_2016-->