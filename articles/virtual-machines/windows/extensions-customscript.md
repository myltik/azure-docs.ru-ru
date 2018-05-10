---
title: Расширение пользовательских сценариев Azure в Windows | Документация Майкрософт
description: Сведения об автоматизации задач настройки виртуальных машин Windows с помощью расширения пользовательских сценариев
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: danis
ms.openlocfilehash: 3a979df6148ae396cf5d9a34dc5f2eb0d455b736
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="custom-script-extension-for-windows"></a>Расширение Custom Script в ОС Windows

Расширение пользовательских сценариев загружает и запускает сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения. Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager, а также его можно запустить с помощью интерфейса командной строки Azure, PowerShell, портала Azure или API REST виртуальной машины Azure.

В этом документе объясняется, как использовать расширение пользовательских сценариев с помощью модуля Azure PowerShell и шаблонов Azure Resource Manager, а также подробно описываются действия по устранению неполадок в системах Windows.

## <a name="prerequisites"></a>предварительным требованиям

> [!NOTE]  
> Не используйте расширение пользовательских сценариев для выполнения команды Update-AzureRmVM на той же виртуальной машине, которая использовалась для параметра, так как команда выполнится через определенный период времени.  
>   
> 

### <a name="operating-system"></a>Операционная система

Расширение пользовательских сценариев для Windows может выполняться для выпусков Windows 10 Client, Windows Server 2008 R2, 2012, 2012 R2 и 2016.

### <a name="script-location"></a>Расположение сценария

Сценарий нужно хранить в хранилище BLOB-объектов Azure или любом другом месте, к которому можно получить доступ по допустимому URL-адресу.

### <a name="internet-connectivity"></a>Подключение к Интернету

Для расширения пользовательских сценариев для Windows требуется, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-schema"></a>Схема расширения

В следующем объекте JSON показана схема для расширения пользовательских сценариев. Для расширения требуется расположение сценария (служба хранилища Azure или другое расположение с допустимым URL-адресом) и команда, которую следует выполнить. При использовании службы хранилища Azure в качестве источника сценария требуется ключ и имя учетной записи хранения Azure. Эти элементы следует рассматривать в качестве конфиденциальных данных. Их нужно задать в защищенной конфигурации параметров расширений. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример |
| ---- | ---- |
| версия_API | 2015-06-15 |
| publisher | Microsoft.Compute; |
| Тип | extensions |
| typeHandlerVersion | 1.9 |
| fileUris (пример) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (пример) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (пример) | examplestorageacct |
| storageAccountKey (пример) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

**Примечание**. В именах свойств учитывается регистр. Используйте имена как показано выше, чтобы избежать проблем с развертыванием.

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения пользовательских сценариев во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе. Пример шаблона, который содержит расширение пользовательских сценариев, можно найти на сайте [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Выполнив команду `Set-AzureRmVMCustomScriptExtension`, расширение пользовательских сценариев можно добавить на существующую виртуальную машину. Дополнительные сведения см. в статье о [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге на целевой виртуальной машине.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Указанные файлы скачиваются в указанный ниже каталог на целевой виртуальной машине.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
`<n>` — десятичное целое число, которое может измениться между выполнениями расширения.  Значение `1.*` соответствует фактическому текущему значению `typeHandlerVersion` расширения.  Например, фактическим каталогом может быть `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

При выполнении команды `commandToExecute` расширение установит этот каталог (например, `...\Downloads\2`) в качестве текущего рабочего каталога. Это позволяет использовать относительные пути для поиска файлов, скачанных с помощью свойства `fileURIs`. Примеры приведены в следующей таблице.

Так как со временем абсолютный путь для скачивания может измениться, в строке `commandToExecute` лучше указывать относительные пути к сценариям или файлам, когда это возможно. Например: 
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Для файлов, скачанных с помощью списка свойств `fileUris`, сохраняются данные пути после первого сегмента универсального кода ресурса (URI).  Как показано в следующей таблице, скачанные файлы распределяются по подкаталогам в соответствии со структурой значений `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Примеры скачанных файлов

| Универсальный код ресурса (URI) в fileUris | Относительное расположение скачанных файлов | Абсолютное расположение скачанных файлов* |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Как было отмечено выше, абсолютные пути каталогов изменятся в течение времени существования виртуальной машины, но не в рамках одного выполнения расширения CustomScript.

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
