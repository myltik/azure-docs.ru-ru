---
title: Расширение виртуальной машины агента Наблюдателя за сетями Azure для Linux | Документация Майкрософт
description: Развертывание агента Наблюдателя за сетями на виртуальной машине Linux с помощью расширения виртуальной машины.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 5a33f183470ec3879344f0cfe335bab38f9ff30f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32778671"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Расширение виртуальной машины агента Наблюдателя за сетями для Linux

## <a name="overview"></a>Обзор

[Наблюдатель за сетями Azure](/azure/network-watcher/) — это служба мониторинга производительности, диагностики и анализа сети, позволяющая наблюдать за сетями Azure. Расширение виртуальной машины агента Наблюдателя за сетями необходимо для некоторых функций Наблюдателя за сетями на виртуальных машинах Azure. Сюда входит запись сетевого трафика по запросу и другие дополнительные функциональные возможности.

В этом документе подробно описаны поддерживаемые платформы и параметры развертывания для расширения виртуальной машины агента Наблюдателя за сетями для Linux. Установка агента не прерывается и не требует перезагрузки виртуальной машины.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="operating-system"></a>Операционная система

Расширение виртуальной машины агента Наблюдателя за сетями можно выполнять на следующих дистрибутивах Linux.

| Дистрибутив | Version (версия) |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS и 12.04 LTS |
| Debian | 7 и 8 |
| RedHat | 6 и 7 |
| Oracle Linux | 6.8 и выше и 7 |
| SUSE Linux Enterprise Server | 11 и 12 |
| OpenSUSE Leap | 42.3 или выше |
| CentOS | 6.5 и выше и 7 |
| CoreOS | 899.17.0 и выше |

Обратите внимание, что CoreOS в данный момент не поддерживается.

### <a name="internet-connectivity"></a>Подключение к Интернету

Для некоторых функциональных возможностей агента Наблюдателя за сетями требуется, чтобы целевая виртуальная машина была подключена к Интернету. Без возможности установить исходящие подключения некоторые возможности агента Наблюдателя за сетями могут работать неправильно или стать недоступными. Дополнительные сведения см. в [документации по Наблюдателю за сетями](/azure/network-watcher/).

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
        "type": "NetworkWatcherAgentLinux",
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
| Тип | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения агента Наблюдателя за сетями во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе.

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Azure CLI можно использовать для развертывания расширения виртуальной машины агента Наблюдателя за сетями на существующей виртуальной машине.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshooting"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете ознакомиться с документацией по Наблюдателю за сетями или обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
