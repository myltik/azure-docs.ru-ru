---
title: Применение пользовательских сценариев на виртуальных машинах Windows с помощью шаблонов | Microsoft Docs
description: Автоматизируйте задачи настройки виртуальных машин Windows с помощью расширения пользовательских сценариев и шаблонов Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap

---
# Использование расширений пользовательских сценариев для виртуальной машины Windows с шаблонами Azure Resource Manager
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Пример шаблона для виртуальной машины Windows
В разделе Resource шаблона определите следующий ресурс.

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

В приведенном выше примере замените URL-адрес и имя файла собственными значениями. После создания шаблон можно развернуть с помощью Azure PowerShell.

Если вы хотите сохранить конфиденциальность URL-адресов и параметров в сценарии, то можете указать, что URL-адрес сценария является **частным**. Если указать URL-адрес сценария как **частный**, то доступ к нему предоставлялся только по имени учетной записи хранения и ключу, которые передаются как защищенные параметры. Параметры сценария могут также предоставляться расширению пользовательских сценариев как защищенные параметры, начиная с версии 1.7 или выше.

## Пример шаблона для виртуальной машины Windows с защищенными параметрами
        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
Сведения о схеме последних версий расширения пользовательских сценариев см. в разделе [Примеры конфигурации расширения виртуальной машины Microsoft Azure](virtual-machines-windows-extensions-configuration-samples.md).

Примеры конфигурации приложений на виртуальной машине с использованием расширения пользовательских сценариев см. в разделе [Расширение пользовательских сценариев на виртуальной машине Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/).

<!---HONumber=AcomDC_0824_2016-->