---
title: "Применение пользовательских сценариев на виртуальных машинах Windows с помощью шаблонов | Документация Майкрософт"
description: "Автоматизируйте задачи настройки виртуальных машин Windows с помощью расширения пользовательских сценариев и шаблонов Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ac63fd346f158d52b359f2d6b398d0d00ea0c67e


---
# <a name="windows-vm-custom-script-extensions-with-azure-resource-manager-templates"></a>Использование расширений пользовательских сценариев для виртуальной машины Windows с шаблонами Azure Resource Manager
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## <a name="template-example-for-a-windows-vm"></a>Пример шаблона для виртуальной машины Windows
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

В приведенном выше примере замените URL-адрес и имя файла собственными значениями.
После создания шаблон можно развернуть с помощью Azure PowerShell.

Если вы хотите сохранить конфиденциальность URL-адресов и параметров в сценарии, то можете указать, что URL-адрес сценария является **частным**. Если указать URL-адрес сценария как **частный**, то доступ к нему предоставлялся только по имени учетной записи хранения и ключу, которые передаются как защищенные параметры. Параметры сценария могут также предоставляться расширению пользовательских сценариев как защищенные параметры, начиная с версии 1.7 или выше.

## <a name="template-example-for-a-windows-vm-with-protected-settings"></a>Пример шаблона для виртуальной машины Windows с защищенными параметрами
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
Сведения о схеме последних версий расширения пользовательских сценариев см. в статье [Примеры конфигурации расширения виртуальной машины Microsoft Azure](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Примеры конфигурации приложений на виртуальной машине с использованием расширения пользовательских сценариев см. в статье [Расширение пользовательских сценариев на виртуальной машине Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/).




<!--HONumber=Nov16_HO3-->


