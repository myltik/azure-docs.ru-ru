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
ms.openlocfilehash: db508e2311602a66a2c252ffaa842f8bfb4f670b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076077"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Расширение виртуальной машины агента Наблюдателя за сетями для Linux

## <a name="overview"></a>Обзор

[Наблюдатель за сетями Azure](/azure/network-watcher/) — это служба мониторинга производительности, диагностики и анализа сети, позволяющая наблюдать за сетями Azure. Расширение виртуальной машины для агента службы "Наблюдатель за сетями" необходимо для некоторых функций этой службы для виртуальных машин Azure, например, для записи трафика по требованию и других дополнительных функций.

В этой статье подробно описаны поддерживаемые платформы и параметры развертывания для расширения виртуальной машины для агента службы "Наблюдатель за сетями" для Linux. Установка агента не прерывает работу и не требует перезагрузки виртуальной машины.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="operating-system"></a>Операционная система

Расширение виртуальной машины для агента службы "Наблюдатель за сетями" можно настроить для следующих дистрибутивов Linux.

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

CoreOS не поддерживается.

### <a name="internet-connectivity"></a>Подключение к Интернету

Для некоторых функциональных возможностей агента службы "Наблюдатель за сетями" требуется, чтобы виртуальная машина была подключена к Интернету. Без возможности установить исходящие подключения некоторые возможности агента службы "Наблюдатель за сетями" могут работать неправильно или стать недоступными. Дополнительные сведения о функциях службы "Наблюдатель за сетями", для которых нужен агент, приведены в [документации по службе "Наблюдатель за сетями"](/azure/network-watcher/).

## <a name="extension-schema"></a>Схема расширения

В следующем коде JSON показана схема для расширения агента Наблюдателя за сетями. Расширение не требует и не поддерживает какие-либо параметры, предоставляемые пользователем. Оно использует собственную конфигурацию по умолчанию.

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

Вы можете развернуть расширения виртуальной машины Azure с помощью шаблона Azure Resource Manager. Чтобы развернуть расширение агента службы "Наблюдатель за сетями", используйте в шаблоне предыдущую схему JSON.

## <a name="azure-cli-10-deployment"></a>Развертывание с использованием Azure CLI 1.0

Следующий пример развертывает расширение виртуальной машины для агента службы "Наблюдатель за сетями" на существующей виртуальной машине, развернутой с помощью классической модели.

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-20-deployment"></a>Развертывание с использованием Azure CLI 2.0

Следующий пример развертывает расширение виртуальной машины для агента службы "Наблюдатель за сетями" на существующей виртуальной машине, развернутой с помощью Resource Manager.

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshooting"></a>Устранение неполадок

Сведения о состоянии развертываний расширения можно получить на портале Azure или при помощи Azure CLI.

В следующем примере показано состояние развертывания расширений для виртуальной машины, развернутой с помощью классической модели и Azure CLI 1.0.

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

В следующем примере показано состояние развертывания расширения NetworkWatcherAgentLinux для виртуальной машины, развернутой с помощью Resource Manager и Azure CLI 2.0.

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете ознакомиться с [документацией по службе "Наблюдатель за сетями"](/azure/network-watcher/) или обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
