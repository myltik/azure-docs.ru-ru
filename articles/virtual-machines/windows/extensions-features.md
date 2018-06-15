---
title: Расширения и компоненты виртуальной машины Windows в Azure | Документация Майкрософт
description: Узнайте о расширениях, доступных для виртуальных машин Azure. Расширения сгруппированы по предоставляемым функциям или улучшениям.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09eb2e723be80fd623ad53c9bda8271ef8846a0f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192440"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Обзор расширений и компонентов виртуальной машины под управлением Windows

Расширения виртуальных машин Azure — это небольшие приложения, которые выполняют задачи настройки и автоматизации после развертывания виртуальных машин Azure. Например, если на виртуальной машине нужно установить программное обеспечение, настроить антивирусную защиту или конфигурацию Docker, это можно сделать с помощью расширения виртуальной машины (далее — расширение ВМ). Расширения ВМ Azure можно запускать с помощью интерфейса командной строки Azure, PowerShell, шаблонов Azure Resource Manager или портала Azure. Расширения можно использовать при развертывании новой виртуальной машины или запускать на любой из существующих систем.

Этот документ содержит обзор расширений виртуальной машины, предварительные требования для их использования, а также рекомендации по обнаружению, управлению и удалению. Этот документ содержит только обобщенные сведения, так как существует множество расширений виртуальной машины с разными параметрами настройки. Информацию о нужном расширении можно найти в соответствующей документации.

## <a name="use-cases-and-samples"></a>Варианты использования и примеры

Существует много разных расширений виртуальной машины Azure, каждое из которых используется в определенных случаях. Вот некоторые варианты использования:

- применение к виртуальной машине конфигураций требуемого состояния PowerShell с помощью расширения DSC для Windows. Подробнее см. [Общие сведения об обработчике расширения Desired State Configuration в Azure](extensions-dsc-overview.md);
- настройка мониторинга виртуальной машины с помощью расширения виртуальной машины Microsoft Monitoring Agent. Подробнее см. [Подключение виртуальных машин Azure к службе Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md);
- настройка мониторинга инфраструктуры Azure с помощью расширения Datadog. Дополнительную информацию см. в [блоге Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/);
- настройка виртуальной машины Azure с помощью Chef. Подробнее см. [Автоматизация развертывания виртуальной машины Azure с помощью Chef](chef-automation.md);

Помимо расширений, созданных для конкретных процессов, существует расширение пользовательских сценариев для виртуальных машин под управлением Windows и Linux. Расширение пользовательских сценариев для Windows позволяет запустить на виртуальной машине любой сценарий PowerShell. Это полезно при разработке развертывания Azure, требующего дополнительной настройки, которую не могут выполнить собственные средства Azure. Подробнее см. [Использование расширений пользовательских сценариев для виртуальной машины Windows с шаблонами Azure Resource Manager](extensions-customscript.md).


## <a name="prerequisites"></a>предварительным требованиям

Каждое расширение виртуальной машины может иметь собственный набор необходимых компонентов. Например, для расширения Docker требуется поддерживаемый дистрибутив Linux. Индивидуальные требования для каждого расширения подробно описаны в соответствующей документации.

### <a name="azure-vm-agent"></a>Агент виртуальной машины Azure
Агент виртуальной машины Azure управляет взаимодействием виртуальной машины с контроллером структуры Azure. Агент отвечает за многие функциональные аспекты развертывания виртуальных машин Azure и управления ими, включая запуск расширений ВМ. Агент виртуальной машины Azure предварительно установлен во всех образах Azure Marketplace. Также его можно установить в поддерживаемых операционных системах.

Сведения о поддерживаемых операционных системах и инструкции по установке см. в статье [Обзор агента и расширений виртуальной машины](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Поиск расширений ВМ
Существует множество различных расширений ВМ, которые можно использовать с виртуальными машинами Azure. Чтобы увидеть полный список, выполните следующую команду в модуле PowerShell Azure Resource Manager. При выполнении этой команды обязательно укажите нужное расположение.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Запуск расширений ВМ

Расширения ВМ Azure можно запускать на существующих виртуальных машинах. Это можно использовать для изменения конфигурации или восстановления подключения на уже развернутой виртуальной машине. Также расширения можно использовать при развертывании с помощью шаблонов Azure Resource Manager. Используя расширения вместе с шаблонами Resource Manager, можно подготовить виртуальные машины Azure к развертыванию и настройке так, чтобы исключить необходимость вмешательства после развертывания.

Запустить расширение на существующей виртуальной машине можно несколькими способами.

### <a name="powershell"></a>PowerShell

Есть несколько команд PowerShell, позволяющих выполнять отдельные модули. Чтобы просмотреть их список, выполните следующие команды PowerShell.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Вы увидите примерно такой результат:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

В следующем примере расширение пользовательских сценариев загружает скрипт из репозитория GitHub на целевую виртуальную машину и запускает этот скрипт. Дополнительные сведения об использовании расширения пользовательских сценариев см. в статье [Использование расширений пользовательских сценариев для виртуальной машины Windows с шаблонами Azure Resource Manager](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

В этом примере расширение доступа к виртуальной машине сбрасывает пароль администратора на виртуальной машине Windows. Дополнительные сведения о расширении доступа к виртуальной машине см. в статье [Сброс службы удаленных рабочих столов или ее пароля для входа в систему на виртуальной машине под управлением Windows](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

С помощью команды `Set-AzureRmVMExtension` можно запустить любое расширение виртуальной машины. Дополнительные сведения см. в разделе справки [о команде AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx).


### <a name="azure-portal"></a>Портал Azure

Расширение виртуальной машины можно выполнить на существующей виртуальной машине с помощью портала Azure. Для этого найдите нужную виртуальную машину, выберите элемент **Расширения** и нажмите кнопку **Добавить**. Вы увидите список доступных расширений. Выберите то, которое вам нужно, и следуйте указаниям мастера.

На следующем рисунке показана установки расширения Microsoft Antimalware с помощью портала Azure.

![Установка расширения защиты от вредоносных программ](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Расширения ВМ можно добавлять в шаблоны Azure Resource Manager. Так они смогут выполняться при развертывании соответствующего шаблона. Развертывание расширений с помощью шаблона позволяет создать полностью настроенные развертывания Azure. Например, ниже представлен код JSON из шаблона Resource Manager, который развертывает набор виртуальных машин с балансировкой нагрузки и базу данных SQL Azure, а затем устанавливает на каждой виртуальной машине приложение .NET Core. Расширение ВМ отвечает за установку программного обеспечения.

Дополнительные сведения вы можете получить, ознакомившись с [полным шаблоном Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Дополнительные сведения см. в статье [Создание шаблонов Azure Resource Manager с помощью расширений виртуальной машины Windows](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Защита данных в расширениях ВМ

При работе с расширением ВМ может использоваться конфиденциальная информация, например учетные данные, имена учетных записей хранения или ключи доступа к учетной записи хранения. Многие расширения ВМ поддерживают защищенную конфигурацию, при использовании которой данные хранятся в зашифрованном виде и расшифровываются только на целевой виртуальной машине. Каждое расширение имеет собственную схему защищенной конфигурации, которая описывается в документации по этим расширениям.

В следующем примере показан экземпляр расширения пользовательских сценариев для Windows. Обратите внимание, что команда для выполнения расширения содержит набор учетных данных. В этом примере выполняемая команда не шифруется.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Чтобы защитить строку выполнения, переместите свойство **выполняемой команды** в **защищенную конфигурацию**.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
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
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Устранение неполадок с расширениями ВМ

Связанные с каждым расширением виртуальной машины неполадки устраняются определенным образом. Например, при использовании расширения пользовательских скриптов вы можете найти информацию о выполнении скрипта на той виртуальной машине, на которой выполнялось расширение. Действия по устранению неполадок для конкретных расширений описаны в документации по соответствующим расширениям.

Описанные ниже действия по устранению неполадок относятся ко всем расширения ВМ.

### <a name="view-extension-status"></a>Просмотр состояния расширения

После того, как расширение виртуальной машины будет выполнено на виртуальной машине, следующая команда PowerShell позволит получить состояние расширения. Замените в примере имена-параметры собственными значениями. Параметр `Name` принимает имя, присвоенное расширению во время выполнения.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Данные результата выглядят следующим образом:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Кроме того, состояние выполнения расширения можно найти на портале Azure. Чтобы просмотреть состояние расширения, выберите виртуальную машину, затем щелкните **Расширения** и найдите нужное расширение.

### <a name="rerun-vm-extensions"></a>Повторный запуск расширений виртуальной машины

В некоторых случаях может потребоваться повторный запуск расширения ВМ. Для этого следует сначала удалить расширение, затем повторно запустить его любым удобным способом. Чтобы удалить расширение, выполните следующую команду в модуле Azure PowerShell. Замените в примере имена-параметры собственными значениями.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Также расширение можно удалить с помощью портала Azure. Для этого выполните следующие действия:

1. Выберите виртуальную машину.
2. Выберите **Расширения**.
3. Выберите нужное расширение.
4. Выберите **Удалить**.

## <a name="common-vm-extensions-reference"></a>Справочники по распространенным расширениям виртуальной машины
| Имя расширения | ОПИСАНИЕ | Дополнительные сведения |
| --- | --- | --- |
| Расширение Custom Script в ОС Windows |Выполняет сценарии на виртуальных машинах Azure. |[Расширение Custom Script в ОС Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Расширение DSC в ОС Windows |Расширение PowerShell DSC (настройка требуемого состояния) |[Общие сведения об обработчике расширения Desired State Configuration в Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Расширение системы диагностики Azure |Управляет системой диагностики Azure |[Расширение системы диагностики Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Расширение Azure VM Access |Управляет пользователями и учетными данными. |[Расширение VM Access для Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
