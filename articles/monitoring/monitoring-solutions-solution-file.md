---
title: Создание файла решения по управлению в Azure | Документация Майкрософт
description: Решения по управлению предоставляют упакованные сценарии управления, которые клиенты могут добавить в свою среду Azure.  В этой статье описано, как создавать решения для управления, которые можно использовать в своей среде или предоставлять другим пользователям.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 46e6ea791752045b0f1afbf1e83e43f498415e54
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887464"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Создание файла решения по управлению в Azure (предварительная версия)
> [!NOTE]
> Это предварительная документация по созданию решений по управлению в Azure, которая доступна в данный момент в режиме предварительной версии. Любые схемы, приведенные ниже, могут измениться.  

Решения по управлению в Azure реализуются как [шаблоны управления ресурсами](../azure-resource-manager/resource-manager-template-walkthrough.md).  Поэтому создание решения для управления — это, по сути, [создание шаблона](../azure-resource-manager/resource-group-authoring-templates.md).  В этой статье подробно описываются шаблоны, используемые для решений, а также способы настойки ресурсов для типичного решения.


## <a name="tools"></a>Средства

Для работы с файлами решения можно использовать любой текстовый редактор, но мы рекомендуем использовать компоненты, предоставляемые в Visual Studio или Visual Studio Code, как описано в следующих статьях.

- [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Работа с шаблонами Azure Resource Manager в Visual Studio Code](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>structure
Базовая структура файла решения для управления аналогична структуре [шаблона Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format) и выглядит следующим образом.  В последующих разделах описаны элементы верхнего уровня и их содержимое в решении.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Параметры
[Параметры](../azure-resource-manager/resource-group-authoring-templates.md#parameters) — это значения, которые пользователь указывает при установке решения для управления.  Существуют стандартные параметры для всех решений. При необходимости можно добавить дополнительные параметры для конкретного решения.  Каким образом пользователи будут указывать значения параметров при установке решения, будет зависеть от конкретного параметра и способа установки решения.

Если пользователь [устанавливает решение по управлению](monitoring-solutions.md#install-a-management-solution) с помощью Azure Marketplace или шаблонов быстрого запуска Azure, ему будет предложено выбрать [рабочую область Log Analytics и учетную запись службы автоматизации](monitoring-solutions.md#log-analytics-workspace-and-automation-account).  Их используют для заполнения значений для каждого из стандартных параметров.  Здесь пользователю совсем не обязательно указывать значения для всех стандартных параметров, а только для дополнительных.


Ниже приведен пример параметра.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

В следующей таблице описываются атрибуты параметра.

| Атрибут | ОПИСАНИЕ |
|:--- |:--- |
| Тип |Тип данных для параметра. Элемент управления вводом, отображаемый для пользователя, зависит от типа данных.<br><br>bool — раскрывающийся список<br>string — текстовое поле<br>int — текстовое поле<br>securestring — поле для ввода пароля<br> |
| category |Необязательная категория для параметра.  Параметры одной категории группируются. |
| control |Дополнительная функция для строковых параметров.<br><br>datetime — отображается элемент управления для даты и времени.<br>guid — значение GUID создается автоматически и параметр не отображается. |
| description |Необязательное описание параметра.  Отображается в информационном всплывающем предупреждении возле параметра. |

### <a name="standard-parameters"></a>Стандартные параметры
В следующей таблице перечислены стандартные параметры для всех решений для управления.  Эти значения заполняются автоматически, а не предлагаются для заполнения пользователем при установке решения из Azure Marketplace или с помощью шаблонов быстрого запуска.  Пользователь должен указать значения для стандартных параметров, если решение устанавливается другим способом.

> [!NOTE]
> Пользовательский интерфейс в Azure Marketplace и шаблонах быстрого запуска ожидает ввода имен параметров, указанных в таблице.  При использовании других имен параметров пользователю будет предложено указать их, они не будут указаны автоматически.
>
>

| Параметр | type | ОПИСАНИЕ |
|:--- |:--- |:--- |
| accountName |строка |Учетная запись службы автоматизации Azure. |
| pricingTier |строка |Ценовая категория рабочей области Log Analytics и учетной записи службы автоматизации Azure. |
| regionId |строка |Регион службы автоматизации Azure. |
| solutionName |строка |Имя решения.  При развертывании решения с использованием шаблонов быстрого запуска следует указать solutionName как параметр, чтобы можно было определить строку и не требовалось, чтобы ее указывал пользователь. |
| workspaceName |строка |Имя рабочей области Log Analytics. |
| workspaceRegionId |строка |Регион рабочего пространства Log Analytics. |


Ниже приведена структура стандартных параметров, которую можно скопировать и вставить в файл решения.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Ссылки на значения параметров в других элементах решения создаются с помощью синтаксиса **parameters('имя_параметра')**.  Например, для доступа к имени рабочей области можно использовать **parameters('имя_рабочей_области')**.

## <a name="variables"></a>Переменные
[Переменные](../azure-resource-manager/resource-group-authoring-templates.md#variables) представляют собой значения, которые будут использоваться в остальной части решения по управлению.  Эти значения невидимы для пользователя, который устанавливает решение.  Их задача — обеспечить пользователя единым расположением для управления значениями, которые в решении могут использоваться многократно. Все значения для решения следует поместить в переменные, а не прямо указывать их в элементе **resources** в коде.  Таким образом код становится более удобным для чтения. Кроме того, в этом случае значения можно легко изменить в более поздних версиях.

Ниже приведен пример элемента **variables** с типичные параметрами, используемыми в решениях.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Обращаться к значениям переменных в рамках решения можно с помощью синтаксиса **variables('имя_переменной')**.  Например, для доступа к переменной SolutionName можно использовать синтаксис **variables('имя_решения')**.

Кроме того, можно определить сложные переменные как несколько наборов значений.  Это особенно удобно в решениях по управлению, где для разных типов ресурсов определяется несколько свойств.  Например, можно изменить структуру переменных решения выше, чтобы они выглядели следующим образом.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

В этом случае обращаться к значениям переменных в рамках решения можно с помощью синтаксиса **variables('имя_переменной').property**.  Например, для доступа к переменной SolutionName можно использовать синтаксис **variables('решение').Name**.

## <a name="resources"></a>Ресурсы
Элемент [Resources](../azure-resource-manager/resource-group-authoring-templates.md#resources) определяет различные ресурсы, которые будет устанавливать и настраивать решение по управлению.  Это самая крупная и сложная часть шаблона.  Структуру и полное описание элементов ресурсов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#resources).  В других статьях этой документации описаны различные ресурсы, которые обычно определяются. 


### <a name="dependencies"></a>Зависимости
Элемент **dependsOn** указывает [зависимость](../azure-resource-manager/resource-group-define-dependencies.md) от другого ресурса.  При установке решения ресурс не создается, пока не будут созданы все его зависимости.  Например, при установке решение может [запустить модуль Runbook](monitoring-solutions-resources-automation.md#runbooks) с помощью [ресурса задания](monitoring-solutions-resources-automation.md#automation-jobs).  Ресурс задания будет зависеть от ресурса модуля Runbook. Таким образом обеспечивается создание модуля Runbook до задания.

### <a name="log-analytics-workspace-and-automation-account"></a>Рабочая область Log Analytics и учетная запись службы автоматизации
Решениям по управлению требуется [рабочая область Log Analytics](../log-analytics/log-analytics-manage-access.md) для хранения представлений и [учетная запись службы автоматизации](../automation/automation-security-overview.md#automation-account-overview) для хранения модулей Runbook и связанных ресурсов.  К ним нужно предоставить доступ перед созданием ресурсов решения. Их не нужно определять в самом решении.  Пользователь указывает [рабочую область и учетную запись](monitoring-solutions.md#log-analytics-workspace-and-automation-account) при развертывании решения. Однако разработчик решения должен учитывать следующие моменты.


## <a name="solution-resource"></a>Ресурс решения
Для каждого решения требуется запись ресурса в элементе **resources**, который определяет само решение.  Ресурс представлен типом **Microsoft.OperationsManagement/solutions** и имеет следующую структуру. К ним относятся [стандартные параметры](#parameters) и [переменные](#variables), которые, как правило, используются для определения свойств решения.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Зависимости
Ресурс решения должен содержать [зависимость](../azure-resource-manager/resource-group-define-dependencies.md) от всех ресурсов решения, так как их нужно создать до решения.  Это можно сделать, добавив запись для каждого ресурса в элемент **dependsOn**.

### <a name="properties"></a>properties
У ресурса решения есть свойства, приведенные в таблице ниже.  Сюда входят ресурсы, на которые решение ссылается и которые оно содержит. Это и определяет, как будет осуществляться управление ресурсом после установки решения.  Каждый ресурс в решении должен быть указан в одном из свойств: **referencedResources** или **containedResources**.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| workspaceResourceId |Идентификатор рабочей области Log Analytics в формате *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<имя_рабочей_области\>*. |
| referencedResources |Список ресурсов решения, которые не будут удалены при удалении решения. |
| containedResources |Список ресурсов решения, которые будут удалены при удалении решения. |

Приведенный выше пример предназначен для решения с модулем Runbook, расписанием и представлением.  На расписание и модуль Runbook *ссылается* элемент **properties**, поэтому они не будут удалены вместе с решением.  Представление *содержится* в решении, поэтому будет удалено при удалении решения.

### <a name="plan"></a>План
Свойства сущности **plan** ресурса решения приведены в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| name |Имя решения. |
| версия |Версия решения, указанная разработчиком. |
| product |Уникальная строка для определения решения. |
| publisher |Издатель решения. |



## <a name="sample"></a>Образец
Примеры файлов решения с ресурсом решения см. в следующих статьях.

- [Ресурсы службы автоматизации](monitoring-solutions-resources-automation.md#sample)
- [Сохраненные поиски и оповещения Log Analytics в решениях OMS (предварительная версия)](monitoring-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Дополнительная информация
* [Добавьте сохраненные поиски и оповещения](monitoring-solutions-resources-searches-alerts.md) в решение для управления.
* [Добавьте представления](monitoring-solutions-resources-views.md) в решение для управления.
* [Добавьте модули Runbook и другие ресурсы службы автоматизации](monitoring-solutions-resources-automation.md) в решение по управлению.
* Узнайте больше о [создании шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Найдите в коллекции [шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates) примеры разных шаблонов Resource Manager.
