---
title: "Расширение виртуальной машины агента Наблюдателя за сетями Azure для Windows | Документация Майкрософт"
description: "Развертывание агента Наблюдателя за сетями на виртуальной машине Windows с помощью расширения виртуальной машины."
services: virtual-machines-windows
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b8d6a998bc86337b286a3434f44f762cca9b7e68
ms.contentlocale: ru-ru
ms.lasthandoff: 03/31/2017

---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины агента Наблюдателя за сетями для Windows

## <a name="overview"></a>Обзор

[Наблюдатель за сетями Azure](https://review.docs.microsoft.com/en-us/azure/network-watcher/) — это служба мониторинга производительности, диагностики и анализа сети, позволяющая наблюдать за сетями Azure. Расширение виртуальной машины агента Наблюдателя за сетями необходимо для некоторых функций Наблюдателя за сетями на виртуальных машинах Azure. Сюда входит запись сетевого трафика по запросу и другие дополнительные функциональные возможности.

В этом документе подробно описаны поддерживаемые платформы и параметры развертывания для расширения виртуальной машины агента Наблюдателя за сетями для Windows.

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>операционная система

Расширение агента Наблюдателя за сетями для Windows может выполняться в выпусках Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 и Windows Server 2016. Обратите внимание, что Nano Server в данный момент не поддерживается.

### <a name="internet-connectivity"></a>Подключение к Интернету

Для некоторых функциональных возможностей агента Наблюдателя за сетями требуется, чтобы целевая виртуальная машина была подключена к Интернету. Без возможности установить исходящие подключения некоторые возможности агента Наблюдателя за сетями могут работать неправильно или стать недоступными. Дополнительные сведения см. в [документации по Наблюдателю за сетями](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Схема расширения

В следующем коде JSON показана схема для расширения агента Наблюдателя за сетями. В настоящее время это расширение не требует вводить и не поддерживает какие-либо пользовательские параметры и зависит от конфигурации по умолчанию.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Значения свойств

| Имя | Значение и пример |
| ---- | ---- |
| версия_API | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения агента Наблюдателя за сетями во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе.

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Команду `Set-AzureRmVMExtension` можно использовать для развертывания расширения виртуальной машины агента Наблюдателя за сетями на существующей виртуальной машине.

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup1" `
                       -Location "WestUS" `
                       -VMName "myVM1" `
                       -Name "networkWatcherAgent" `
                       -Publisher "Microsoft.Azure.NetworkWatcher" `
                       -Type "NetworkWatcherAgentWindows" `
                       -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshooting"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в модуле Azure PowerShell.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете ознакомиться с руководством пользователя для Наблюдателя за сетями или обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/en-us/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).

