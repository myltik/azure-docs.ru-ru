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
ms.openlocfilehash: 68855e0070916dc672914fbc8ca3587a5d3c25f6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины агента Наблюдателя за сетями для Windows

## <a name="overview"></a>Обзор

[Azure Наблюдатель сети](../../network-watcher/network-watcher-monitoring-overview.md) является сетевой производительности наблюдения, диагностики и аналитика службой, которая обеспечивает наблюдение за сетями Azure. Расширение виртуальной машины агента Наблюдатель сети является обязательным для записи сетевого трафика по требованию и другие дополнительные функциональные возможности на виртуальных машинах Azure.


В этом документе подробно описаны поддерживаемые платформы и параметры развертывания для расширения виртуальной машины агента Наблюдателя за сетями для Windows.

## <a name="prerequisites"></a>Технические условия

### <a name="operating-system"></a>Операционная система

Расширение агента Наблюдателя за сетями для Windows может выполняться в выпусках Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 и Windows Server 2016. Nano Server не поддерживается.

### <a name="internet-connectivity"></a>Подключение к Интернету

Для некоторых функциональных возможностей агента Наблюдателя за сетями требуется, чтобы целевая виртуальная машина была подключена к Интернету. Не может устанавливать исходящие подключения сетевой агент наблюдатель не сможете загружать захват пакетов для вашей учетной записи хранилища. Дополнительные сведения см. в [документации по Наблюдателю за сетями](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Схема расширения

В следующем коде JSON показана схема для расширения агента Наблюдателя за сетями. Расширения ни требуется, и не поддерживает все пользовательские параметры и зависит от конфигурации по умолчанию.

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

| ИМЯ | Значение и пример |
| ---- | ---- |
| версия_API | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| Тип | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Развертывание шаблона

Вы можете развернуть расширений ВМ Azure с помощью шаблонов диспетчера ресурсов Azure. Схема JSON, описанные в предыдущем разделе, в шаблоне диспетчера ресурсов Azure можно использовать для запуска агента Наблюдатель сети расширения во время развертывания шаблона диспетчера ресурсов Azure.

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Используйте `Set-AzureRmVMExtension` команду, чтобы развернуть расширение агента Наблюдатель сети виртуальной машины на существующей виртуальной машины:

```powershell
Set-AzureRmVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshooting"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure и PowerShell. Чтобы просмотреть состояние развертывания расширений для данной виртуальной Машины, выполните следующую команду с помощью модуля Azure PowerShell:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете ознакомиться с руководством пользователя для Наблюдателя за сетями или обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/en-us/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
