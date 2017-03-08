---
title: "Создание решений для управления в Operations Management Suite (OMS) | Документация Майкрософт"
description: "Решения для управления расширяют функции консоли Operations Management Suite (OMS), предоставляя упакованные сценарии управления, которые клиенты могут добавить в свое рабочее пространство OMS.  В этой статье описано, как создавать решения для управления, которые можно использовать в своей среде или предоставлять другим пользователям."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: fc8b76bf996060e226ac3f508a1ecffca6fc3c98
ms.openlocfilehash: caa2f96d452174ebb13c5cbf67737f20e2a2134d


---
# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Создание решений для управления в Operations Management Suite (OMS) (предварительная версия)
> [!NOTE]
> Это предварительная документация для создания решений для управления в консоли OMS, которая доступна в данный момент в режиме предварительной версии. Любые схемы, приведенные ниже, могут измениться.  
>
>

Решения для управления расширяют функции консоли Operations Management Suite (OMS), предоставляя упакованные сценарии управления, которые клиенты могут добавить в свое рабочее пространство OMS.  Эта статья содержит сведения о том, как создать собственные решения для управления, которые можно использовать в своей среде или предоставлять клиентам в сообществе.

## <a name="planning-your-management-solution"></a>Планирование решения для управления
Решения для управления в OMS включают несколько ресурсов, которые поддерживают определенный сценарий управления.  При планировании решения следует сосредоточиться на внедряемом сценарии и всех ресурсах, которые необходимы для его реализации.  Каждое решение должно быть автономным. Оно определяет все необходимые ему ресурсы, даже если один или несколько ресурсов также определены другими решениями.  Для установленного решения для управления все ресурсы создаются, только если они не существуют. При этом можно определить, что произойдет с ресурсом при удалении решения.  

Например, решение для управления может включать [модуль Runbook службы автоматизации Azure](../automation/automation-intro.md), который собирает данные в репозиторий Log Analytics с помощью [расписания](../automation/automation-schedules.md) и [представления](../log-analytics/log-analytics-view-designer.md) для отображения собранных данных разными способами.  Это же расписание может использоваться другим решением.  Разработчик решения для управления определяет все три ресурса, настраивая автоматическое удаление модуля Runbook и представления при удалении решения.    Также будет определено и расписание. Но здесь вам нужно указать, что это расписание не будет удалено вместе с решением, если оно используется другим ресурсом.

## <a name="management-solution-files"></a>Файлы решения для управления
Решения для управления в OMS реализуются как [шаблоны управления ресурсами](../azure-resource-manager/resource-manager-template-walkthrough.md).  Поэтому создание решения для управления — это, по сути, [создание шаблона](../azure-resource-manager/resource-group-authoring-templates.md).  В этой статье подробно описываются шаблоны, используемые для решений, а также способы определения ресурсов для типичного решения.

Базовая структура файла решения для управления аналогична структуре [шаблона Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format) и выглядит следующим образом.  В следующих разделах описаны элементы верхнего уровня и их содержимое в решении.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Параметры
[Параметры](../azure-resource-manager/resource-group-authoring-templates.md#parameters) — это значения, которые пользователь указывает при установке решения для управления.  Существуют стандартные параметры для всех решений. При необходимости можно добавить дополнительные параметры для конкретного решения.  Каким образом пользователи будут указывать значения параметров при установке решения, будет зависеть от конкретного параметра и способа установки решения.

Если пользователь устанавливает решение для управления с помощью [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) или [шаблонов быстрого запуска Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), ему будет предложено выбрать [рабочую область OMS и учетную запись службы автоматизации](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Их используют для заполнения значений для каждого из стандартных параметров.  Здесь пользователю совсем не обязательно указывать значения для всех стандартных параметров, а только для дополнительных.

При установке решения [другим способом](operations-management-suite-solutions.md#finding-and-installing-management-solutions) пользователю нужно указать значения для всех стандартных и дополнительных параметров.

Ниже приведен пример параметра.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

В следующей таблице описываются атрибуты параметра.

| Атрибут | Description (Описание) |
|:--- |:--- |
| type |Тип данных для параметра. Элемент управления вводом, отображаемый для пользователя, зависит от типа данных.<br><br>bool — раскрывающийся список<br>string — текстовое поле<br>int — текстовое поле<br>securestring — поле для ввода пароля<br> |
| category |Необязательная категория для параметра.  Параметры одной категории группируются. |
| control |Дополнительная функция для строковых параметров.<br><br>datetime — отображается элемент управления для даты и времени.<br>guid — значение GUID создается автоматически и параметр не отображается. |
| Описание |Необязательное описание параметра.  Отображается в информационном всплывающем предупреждении возле параметра. |

### <a name="standard-parameters"></a>Стандартные параметры
В следующей таблице перечислены стандартные параметры для всех решений для управления.  Эти значения заполняются автоматически, а не предлагаются для заполнения пользователем при установке решения из Azure Marketplace или с помощью шаблонов быстрого запуска.  Пользователь должен указать значения для стандартных параметров, если решение устанавливается другим способом.

> [!NOTE]
> Пользовательский интерфейс в Azure Marketplace и шаблонах быстрого запуска ожидает ввода имен параметров, указанных в таблице.  При использовании других имен параметров пользователю будет предложено указать их, они не будут указаны автоматически.
>
>

| Параметр | Тип | Description (Описание) |
|:--- |:--- |:--- |
| accountName |строка |Учетная запись службы автоматизации Azure. |
| pricingTier |string |Ценовая категория рабочей области Log Analytics и учетной записи службы автоматизации Azure. |
| regionId |строка |Регион службы автоматизации Azure. |
| solutionName |строка |Имя решения. |
| workspaceName |строка |Имя рабочей области Log Analytics. |
| workspaceRegionId |строка |Регион рабочего пространства Log Analytics. |

### <a name="sample"></a>Образец
Далее указан пример сущности параметра для решения.  Он включает все стандартные параметры и два дополнительных параметра в одной категории.

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
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Ссылки на значения параметров в других элементах решения создаются с помощью синтаксиса **parameters('имя_параметра')**.  Например, для доступа к имени рабочей области можно использовать **parameters('имя_рабочей_области')**.

## <a name="variables"></a>Переменные
Элемент **variables** включает значения, которые будут использоваться в остальной части решения для управления.  Эти значения невидимы для пользователя, который устанавливает решение.  Их задача — обеспечить пользователя единым расположением для управления значениями, которые в решении могут использоваться многократно. Все значения для решения следует поместить в переменные, а не прямо указывать их в элементе **resources** в коде.  Таким образом код становится более удобным для чтения. Кроме того, в этом случае значения можно легко изменить в более поздних версиях.

Ниже приведен пример элемента **variables** с типичные параметрами, используемыми в решениях.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Обращаться к значениям переменных в рамках решения можно с помощью синтаксиса **variables('имя_переменной')**.  Например, для доступа к переменной SolutionName можно использовать синтаксис **variables('имя_решения')**.

## <a name="resources"></a>Ресурсы
Элемент **resources** определяет различные ресурсы, включенные в решение для управления.  Это самая крупная и сложная часть шаблона.  Ресурсы определяются с помощью следующей структуры:  

    "resources": [
        {
            "name": "<name-of-the-resource>",            
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",        
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Зависимости
Элемент **dependsOn** указывает [зависимость](../azure-resource-manager/resource-group-define-dependencies.md) от другого ресурса.  При установке решения ресурс не создается, пока не будут созданы все его зависимости.  Например, при установке решение может [запустить модуль Runbook](operations-management-suite-solutions-resources-automation.md#runbooks) с помощью [ресурса задания](operations-management-suite-solutions-resources-automation.md#automation-jobs).  Ресурс задания будет зависеть от ресурса модуля Runbook. Таким образом обеспечивается создание модуля Runbook до задания.

### <a name="oms-workspace-and-automation-account"></a>Рабочая область OMS и учетная запись службы автоматизации
Решения для управления требуют [рабочую область OMS](../log-analytics/log-analytics-manage-access.md) для хранения представлений и [учетную запись службы автоматизации](../automation/automation-security-overview.md#automation-account-overview) для хранения модулей Runbook и связанных ресурсов.  К ним нужно предоставить доступ перед созданием ресурсов решения. Их не нужно определять в самом решении.  Пользователь указывает [рабочую область и учетную запись](operations-management-suite-solutions.md#oms-workspace-and-automation-account) при развертывании решения. Однако разработчик решения должен учитывать следующие моменты.

## <a name="solution-resource"></a>Ресурс решения
Для каждого решения требуется запись ресурса в элементе **resources**, который определяет само решение.  Ресурс представлен типом **Microsoft.OperationsManagement/solutions**.  Ниже приведен пример ресурса решения.  В следующих разделах описываются его различающиеся элементы.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Имя решения
Имя решения должно быть уникальным в подписке Azure. Ниже представлены рекомендуемые значения, которые следует использовать.  Далее используется переменная с именем **SolutionName** в качестве базового имени и параметр **workspaceName**, чтобы обеспечить уникальность имени.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

В результате выполнения этого примера будет получено следующее имя.

    My Solution Name [MyWorkspace]


### <a name="dependencies"></a>Зависимости
Ресурс решения должен содержать [зависимость](../azure-resource-manager/resource-group-define-dependencies.md) от всех ресурсов решения, так как их нужно создать до решения.  Это можно сделать, добавив запись для каждого ресурса в элемент **dependsOn**.

### <a name="properties"></a>Свойства
У ресурса решения есть свойства, приведенные в таблице ниже.  Сюда входят ресурсы, на которые решение ссылается и которые оно содержит. Это и определяет, как будет осуществляться управление ресурсом после установки решения.  Каждый ресурс в решении должен быть указан в одном из свойств: **referencedResources** или **containedResources**.

| Свойство | Описание |
|:--- |:--- |
| workspaceResourceId |Идентификатор рабочей области OMS в формате *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<имя_рабочей_области\>*. |
| referencedResources |Список ресурсов решения, которые не будут удалены при удалении решения. |
| containedResources |Список ресурсов решения, которые будут удалены при удалении решения. |

Приведенный выше пример предназначен для решения с модулем Runbook, расписанием и представлением.  На расписание и модуль Runbook *ссылается* элемент **properties**, поэтому они не будут удалены вместе с решением.  Представление *содержится* в решении, поэтому будет удалено при удалении решения.

### <a name="plan"></a>План
Свойства сущности **plan** ресурса решения приведены в таблице ниже.

| Свойство | Description (Описание) |
|:--- |:--- |
| name |Имя решения. |
| версия |Версия решения, указанная разработчиком. |
| product |Уникальная строка для определения решения. |
| publisher |Издатель решения. |

## <a name="other-resources"></a>Другие ресурсы:
Дополнительные сведения и примеры ресурсов, которые обычно используются в решениях для управления, см. в следующих статьях.

* [Views and dashboards](operations-management-suite-solutions-resources-views.md) (Представления и панели мониторинга)
* [Automation resources](operations-management-suite-solutions-resources-automation.md) (Ресурсы службы автоматизации)

## <a name="testing-a-management-solution"></a>Тестирование решения для управления
Перед развертыванием решение для управления рекомендуется протестировать с помощью командлета [Test-AzureRmResourceGroupDeployment](../azure-resource-manager/resource-group-template-deploy.md#deploy).  Так вы сможете проверить файл решения, чтобы обнаружить проблемы до попытки развертывания.

## <a name="next-steps"></a>Дальнейшие действия
* [Добавьте сохраненные поиски и оповещения](operations-management-suite-solutions-resources-searches-alerts.md) в решение для управления.
* [Добавьте представления](operations-management-suite-solutions-resources-views.md) в решение для управления.
* [Добавьте модули Runbook и другие ресурсы службы автоматизации](operations-management-suite-solutions-resources-automation.md) в решение для управления.
* Узнайте больше о [создании шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Найдите в коллекции [шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates) примеры разных шаблонов Resource Manager.



<!--HONumber=Jan17_HO4-->


