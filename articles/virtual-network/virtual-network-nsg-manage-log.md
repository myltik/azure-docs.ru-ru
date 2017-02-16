---
title: "Мониторинг операций, событий и счетчиков для групп безопасности сети | Документация Майкрософт"
description: "Включение ведения журналов счетчиков, событий и операций для групп безопасности сети"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 87755571f560f0b41baabac0dc2c437b2738e75f
ms.openlocfilehash: e5f0bdeaf1c29e9f8fc844d4c3d85b1f2595d03b


---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Аналитика журналов для групп безопасности сети

Для групп безопасности сети (NSG) можно включить следующие категории диагностических журналов:

* **Журналы событий.** Содержат записи, по которым правила NSG на основе MAC-адреса применяются к виртуальным машинам и экземплярам ролей. Состояние этих правил регистрируется каждые 60 секунд.
* **Журналы счетчиков**. Содержат записи с информацией о том, сколько раз каждое правило NSG было применено для запрета или разрешения трафика.

> [!NOTE]
> Журналы диагностики доступны только для NSG, для которых применена модель развертывания с помощью Azure Resource Manager. Вы не сможете включить ведение журналов диагностики для NSG, развернутых с помощью классической модели развертывания. Чтобы получить более полное представление об этих двух моделях, см. статью [о моделях развертывания Azure](../resource-manager-deployment-model.md).

Ведение журнала действий (ранее он назывался журналом аудита или рабочим журналом) включена по умолчанию для всех NSG, независимо от модели развертывания. Чтобы найти в журнале действий, какие операции выполнялись в группе безопасности сети, отберите записи со следующими типами ресурсов: Microsoft.ClassicNetwork/networkSecurityGroups, Microsoft.ClassicNetwork/networkSecurityGroups/securityRules, Microsoft.Network/networkSecurityGroups и Microsoft.Network/networkSecurityGroups/securityRules. Подробнее журнал действий Azure описан в [этой статье](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). 

## <a name="enable-diagnostic-logging"></a>Включение ведения журналов диагностики

Ведение журналов диагностики должно быть включено *для всех* NSG, по которым вы намерены собирать данные. Статья [Обзор журналов диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) расскажет вам, куда можно отправлять журналы диагностики. Если у вас нет существующей NSG, выполните инструкции в статье [Как управлять сетевыми группами безопасности с помощью портала предварительной версии](virtual-networks-create-nsg-arm-pportal.md). Ведение журнала диагностики для NSG можно включить любым из следующих методов.

### <a name="azure-portal"></a>Портал Azure

Чтобы включить ведение журнала, войдите на [портал](https://portal.azure.com). Щелкните **дополнительные службы**, затем введите *группы безопасности сети*. Выберите NSG, для которой хотите включить ведение журнала. Выполните инструкции, предложенные в статье [Включение журналов диагностики на портале](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-in-the-portal) для невычислительных ресурсов. Выберите категории журналов: **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter** или обе сразу.

### <a name="powershell"></a>PowerShell

Чтобы включить ведение журналов с помощью PowerShell, выполните инструкции из статьи [Включение журналов диагностики с помощью PowerShell](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-via-powershell). Прежде чем выполнять команды, указанные в этой статье, получите следующую информацию.

- Значение для параметра `-ResourceId` можно получить, заменив значения в квадратных скобках правильными значениями и выполнив такую команду: `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]` Идентификатор, полученный в результате ее выполнения, будет выглядеть примерно так: */subscriptions/ [идентификатор_подписки]/resourceGroups/[группа_ресурсов]/providers/Microsoft.Network/networkSecurityGroups/[имя_NSG]*.
- Если журнал нужно собирать только для одной категории, добавьте в конец предложенной команды параметр `-Categories [category]`, где в качестве значения категории укажите *NetworkSecurityGroupEvent* или *NetworkSecurityGroupRuleCounter*. Если вы не укажете параметр `-Categories`, сбор данных будет включен для обеих категорий журнала.

### <a name="azure-command-line-interface-cli"></a>интерфейс командной строки Azure (CLI)

Чтобы включить ведение журналов с помощью CLI, выполните инструкции из статьи [Включение журналов диагностики с помощью интерфейса командной строки](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-via-cli). Прежде чем выполнять команды, указанные в этой статье, получите следующую информацию.

- Значение для параметра `-ResourceId` можно получить, заменив значения в квадратных скобках правильными значениями и выполнив такую команду: `azure network nsg show [resource-group-name] [nsg-name]` Идентификатор, полученный в результате ее выполнения, будет выглядеть примерно так: */subscriptions/ [идентификатор_подписки]/resourceGroups/[группа_ресурсов]/providers/Microsoft.Network/networkSecurityGroups/[имя_NSG]*.
- Если журнал нужно собирать только для одной категории, добавьте в конец предложенной команды параметр `-Categories [category]`, где в качестве значения категории укажите *NetworkSecurityGroupEvent* или *NetworkSecurityGroupRuleCounter*. Если вы не укажете параметр `-Categories`, сбор данных будет включен для обеих категорий журнала.

## <a name="logged-data"></a>Собранные данные журнала

Данные в оба журнала записываются в формате JSON. Конкретные данные для каждого типа журнала указаны в следующих разделах.

### <a name="event-log"></a>Журнал событий
Этот журнал содержит сведения о том, какие правила NSG на основе MAC-адреса применяются к виртуальным машинам и экземплярам ролей. Для каждого события регистрируются данные, представленные в этом примере:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>Журнал счетчиков правил

Этот файл журнала содержит сведения обо всех случаях применения правил к ресурсам. Для каждого применения правила регистрируются данные, представленные в этом примере:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Просмотр и анализ журналов

Методы просмотра журнала действий описаны в [этой статье](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Методы просмотра журнала диагностики описаны в статье [Обзор журналов диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Если вы отправляете данные диагностики в Log Analytics, [Решение для анализа сетей Azure (предварительная версия) в Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md#use-azure-networking-analytics) поможет получить более подробный анализ данных. 



<!--HONumber=Jan17_HO1-->


