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
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 80f9ecd40c5b9504a6554b95bf374046d8253933
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809783"
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

Расширение настраиваемых скриптов для Linux будет работать в поддерживаемых им ОС. Дополнительные сведения см. в этой [статье](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Расположение сценария

С помощью расширения можно получить доступ к хранилищу BLOB-объектов Azure с помощью учетных данных службы хранилища BLOB-объектов Azure. Кроме того, скрипт может находиться в любом расположении при условии, что виртуальная машина может осуществлять маршрутизацию в эту конечную точку, такую как GitHub, внутренний файловый сервер и т. д.


### <a name="internet-connectivity"></a>Подключение к Интернету
Чтобы скачать скрипт из внешнего источника, например с сайта GitHub или из службы хранилища Azure, откройте дополнительные порты брандмауэра или группы безопасности сети. Например, если скрипт находится в службе хранилища Azure, вы можете разрешить доступ с помощью тегов службы Azure NSG для [хранилища](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Если скрипт расположен на локальном сервере, вам по-прежнему может потребоваться открыть дополнительные порты брандмауэра или группы безопасности сети.

### <a name="tips-and-tricks"></a>Советы и рекомендации
* Высокий процент сбоев этого расширения связан с синтаксическими ошибками в скрипте. Чтобы упростить поиск точки сбоя, протестируйте запуски скрипта без ошибок и включите в скрипт дополнительные возможности ведения журнала.
* Пишите идемпотентные скрипты, чтобы их случайные повторные запуски не приводили к изменениям системы.
* Выполняемые скрипты не должны запрашивать ввод данных пользователем.
* На выполнение скрипта отводится 90 минут. Более продолжительное действие вызовет сбой подготовки расширения.
* Не следует помещать перезагрузки в скрипт, так как будут возникать проблемы с другими устанавливаемыми расширениями и после перезагрузки расширение прекратит работать. 
* Если у вас есть скрипт, приводящий к перезагрузке, установите приложения, запустите скрипты и т. д. Необходимо запланировать перезагрузку с помощью запланированной задачи Windows или таких средств, как расширения DSC, Chef или Puppet.
* Расширение позволяет запустить скрипт только один раз. Если нужно выполнять скрипт при каждой загрузке, создайте запланированную задачу Windows с помощью расширения.
* Чтобы запланировать время выполнения скрипта, необходимо создать запланированную задачу Windows с помощью расширения. 
* Во время выполнения скрипта на портале Azure или в CLI отобразятся только сведения о переходном состоянии расширения. Если нужно чаще обновлять состояние выполняющегося скрипта, создайте собственное решение.
* Расширение настраиваемых скриптов не имеет собственной поддержки прокси-серверов. Но можно использовать средство передачи файлов, которое поддерживает прокси-серверы в скрипте, например *Curl*. 
* Следует учитывать настраиваемые расположения каталогов, которые могут использоваться скриптами или командами, и иметь в распоряжении логику для обработки таких случаев.


## <a name="extension-schema"></a>Схема расширения

В конфигурации расширения пользовательских сценариев указываются такие параметры, как расположение сценария и команда для его выполнения. Эту конфигурацию можно хранить в файлах конфигурации, указать в командной строке или в шаблоне Azure Resource Manager. 

Конфиденциальные данные можно хранить в зашифрованном виде в защищенной конфигурации, которая расшифровывается только внутри виртуальной машины. Защищенную конфигурацию можно использовать для команд, которые содержат секретные данные, например пароли.

Эти элементы следует рассматривать в качестве конфиденциальных данных. Их нужно задать в защищенной конфигурации параметров расширений. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине.

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
**Примечание.** На виртуальной машине нельзя устанавливать больше одной версии расширения одновременно. Если указать пользовательский скрипт дважды в одном и том же шаблоне Resource Manager для той же виртуальной машины, возникнет ошибка. 

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2015-06-15 | дата |
| publisher | Microsoft.Compute; | строка |
| Тип | CustomScriptExtension | строка |
| typeHandlerVersion | 1.9 | int |
| fileUris (пример) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| commandToExecute (пример) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | строка |
| storageAccountName (пример) | examplestorageacct | строка |
| storageAccountKey (пример) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | строка |

>[!NOTE]
>В именах свойств учитывается регистр. Чтобы избежать проблем с развертыванием, используйте имена, как показано ниже.

#### <a name="property-value-details"></a>Сведения о значениях свойств
 * `commandToExecute`: (**обязательное**, строка) выполняемый сценарий точки входа. Если команда содержит секретные данные, например пароли, или если значения fileUri являются конфиденциальным, используйте взамен это поле.
* `fileUris`: (необязательное, строковый массив) URL-адреса файлов для скачивания.
* `storageAccountName`: (необязательное, строка) имя учетной записи хранения. Если указаны учетные данные хранилища, все значения `fileUris` должны иметь формат URL-адресов для BLOB-объектов Azure.
* `storageAccountKey`: (необязательное, строка) ключ доступа для учетной записи хранения.

Указанные ниже значения можно задавать либо в открытых, либо в защищенных параметрах. Расширение отклонит любую конфигурацию, в которой приведенные ниже значения будут указаны в открытых и защищенных параметрах одновременно.
* `commandToExecute`

Открытые параметры могут быть полезны для отладки, но настоятельно рекомендуется использовать защищенные параметры.

Открытые параметры отправляются в виде открытого текста на виртуальную машину, где будет выполняться скрипт.  Защищенные параметры шифруются с помощью ключа, который известен только Azure и виртуальной машине. Параметры сохраняются на виртуальной машине в том виде, в каком они были отправлены, т. е. если параметры были зашифрованы, они сохраняются на виртуальной машине зашифрованными. Сертификат, используемый для расшифровки зашифрованных значений, хранится на виртуальной машине и применяется для расшифровки параметров (при необходимости) во время выполнения.

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
## <a name="further-examples"></a>Дополнительные примеры

### <a name="using-multiple-script"></a>Использование нескольких скриптов
В этом примере есть три скрипта, которые используются для создания сервера. Команда "commandToExecute" вызывает первый скрипт. Затем вы можете выбрать способ вызова других скриптов. Например, можно использовать главный скрипт, управляющий выполнением, с надлежащей обработкой ошибок, ведением журнала и управлением состояниями.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Запуск скриптов из локальной общей папки
В этом примере, возможно, вам потребуется использовать локальный сервер SMB в качестве расположения скрипта. Обратите внимание, что дополнительные параметры передавать не нужно, за исключением *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Многократное выполнение пользовательского скрипта с помощью CLI
Чтобы выполнить расширение пользовательского скрипта более одного раза, должны соблюдаться следующие условия:
1. Параметр Name расширения совпадает с указанным при предыдущем развертывании расширения.
2. Необходимо обновить конфигурацию, иначе команда не будет выполнена повторно. Например, можно добавить в команду динамическое свойство, такое как метка времени. 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду:

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

При выполнении команды `commandToExecute` расширение устанавливает этот каталог (например, `...\Downloads\2`) в качестве текущего рабочего каталога. Это позволяет использовать относительные пути для поиска файлов, скачанных с помощью свойства `fileURIs`. Примеры приведены в следующей таблице.

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

\* Как было отмечено выше, абсолютные пути каталогов изменяются в течение времени существования виртуальной машины, но не в рамках одного выполнения расширения CustomScript.

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
