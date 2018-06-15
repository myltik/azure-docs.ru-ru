---
title: Ресурсы службы автоматизации Azure в решениях по управлению | Документация Майкрософт
description: Как правило, решения по управлению содержат модули Runbook службы автоматизации Azure для автоматизации процессов, таких как сбор и обработка данных мониторинга.  В этой статье описывается, как добавлять модули Runbook и связанные с ними ресурсы в решение.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7f32cc47c96f6fc8adaedef8f8ea3156ee70115
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887474"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Добавление ресурсов службы автоматизации Azure в решение по управлению (предварительная версия)
> [!NOTE]
> Это предварительная версия документации по созданию решений по управлению, доступных в режиме предварительной версии. Любые схемы, приведенные ниже, могут измениться.   


Как правило, [решения по управлению]( monitoring-solutions.md) содержат модули Runbook службы автоматизации Azure для автоматизации процессов, таких как сбор и обработка данных мониторинга.  Помимо модулей Runbook учетные записи службы автоматизации содержат ресурсы, такие как переменные и расписания, поддерживающие модули Runbook, используемые в решении.  В этой статье описывается, как добавлять модули Runbook и связанные с ними ресурсы в решение.

> [!NOTE]
> В примерах этой статьи используются обязательные или общие параметры и переменные для решений по управлению, описанные в статье [Проектирование и сборка решения по управлению в Azure (предварительная версия)]( monitoring-solutions-creating.md). 


## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что вы уже знакомы со следующими сведениями.

- Как [создать решение по управлению]( monitoring-solutions-creating.md).
- Структура [файла решения]( monitoring-solutions-solution-file.md).
- Как [создавать шаблоны Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="automation-account"></a>Учетная запись службы автоматизации
Все ресурсы в службе автоматизации Azure содержатся в [учетной записи службы автоматизации](../automation/automation-security-overview.md#automation-account-overview).  Как указано в разделе [Log Analytics workspace and Automation account]( monitoring-solutions.md#log-analytics-workspace-and-automation-account) (Рабочая область Log Analytics и учетная запись службы автоматизации), учетная запись службы автоматизации не включена в решение для управления и должна быть создана до установки решения.  Если она недоступна, произойдет сбой установки решения.

Имя учетной записи службы автоматизации указывается в имени каждого ресурса службы автоматизации.  Оно указывается в решении с помощью параметра **accountName**, как в следующем примере ресурса модуля Runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Модули Runbook
В файл решения следует включить все модули Runbook, используемые решением, чтобы они создавались при установке решения.  Однако в шаблон нельзя добавить текст модуля Runbook, поэтому следует опубликовать модуль Runbook в общедоступном месте, где к нему может обратиться любой пользователь, устанавливающий решение.

Тип ресурсов [модуля Runbook службы автоматизации Azure](../automation/automation-runbook-types.md) — **Microsoft.Automation/automationAccounts/runbooks**. Их структура приведена ниже. Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


В следующей таблице описаны свойства модулей Runbook.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| runbookType |Указывает тип модуля Runbook. <br><br> Script — сценарий PowerShell <br>PowerShell — рабочий процесс PowerShell <br> GraphPowerShell — графический модуль сценария PowerShell <br> GraphPowerShellWorkflow — графический модуль Runbook рабочего процесса PowerShell |
| logProgress |Указывает, нужно ли создавать [записи о ходе выполнения](../automation/automation-runbook-output-and-messages.md) для модуля Runbook. |
| logVerbose |Указывает, нужно ли создавать [подробные записи](../automation/automation-runbook-output-and-messages.md) для модуля Runbook. |
| description |Необязательное описание модуля Runbook. |
| publishContentLink |Указывает содержимое модуля Runbook. <br><br>uri — URI содержимого модуля Runbook.  Это будет PS1-файл для модулей Runbook PowerShell и сценариев, а также файл экспортированного графического модуля Runbook для графического модуля Runbook.  <br> version — версия модуля Runbook для собственного отслеживания. |


## <a name="automation-jobs"></a>Задания службы автоматизации
При запуске модуля Runbook в службе автоматизации Azure создается задание службы автоматизации.  Ресурс задания службы автоматизации можно добавить в решение для автоматического запуска модуля Runbook при установке решения по управлению.  Этот метод обычно используется для запуска модулей Runbook, которые используются для начальной настройки решения.  Для запуска модуля Runbook через регулярные интервалы создайте [расписание](#schedules) и [расписание заданий](#job-schedules)

Тип ресурсов задания — **Microsoft.Automation/automationAccounts/jobs**. Их структура приведена ниже.  Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

В следующей таблице описаны свойства заданий службы автоматизации.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| runbook |Одна сущность name с именем модуля Runbook для запуска. |
| parameters |Сущность для каждого значения параметра, необходимого для модуля Runbook. |

Задание включает в себя имя модуля Runbook и все значения параметров, которые нужно отправить в модуль Runbook.  Задание должно [зависеть от]( monitoring-solutions-solution-file.md#resources) модуля Runbook, который оно запускает, так как модуль Runbook нужно создать до задания.  При наличии нескольких модулей Runbook, которые нужно запустить, порядок их запуска можно определить, создав зависимость между заданием и другими заданиями, которые должны выполняться в первую очередь.

Имя ресурса задания должно содержать GUID, который обычно назначается с помощью параметра.  Дополнительные сведения о параметрах GUID см. в [этом разделе]( monitoring-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Сертификаты
Тип [сертификатов службы автоматизации Azure](../automation/automation-certificates.md) — **Microsoft.Automation/automationAccounts/certificates**. Их структура приведена ниже. Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



В следующей таблице описаны свойства ресурсов сертификатов.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| base64Value |Значение Base 64 для сертификата. |
| thumbprint |Отпечаток сертификата. |



## <a name="credentials"></a>Учетные данные
Тип [учетных данных службы автоматизации Azure](../automation/automation-credentials.md) — **Microsoft.Automation/automationAccounts/credentials**. Их структура приведена ниже.  Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

В следующей таблице описаны свойства ресурсов учетных записей.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| userName |Имя пользователя для учетных данных. |
| password |Пароль для учетных данных. |


## <a name="schedules"></a>Расписания
Тип [расписаний службы автоматизации Azure](../automation/automation-schedules.md) — **Microsoft.Automation/automationAccounts/schedules**. Их структура приведена ниже. Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

В следующей таблице описаны свойства ресурсов расписания.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| description |Необязательное описание расписания. |
| startTime |Указывает время начала расписания в виде объекта DateTime. Строка указывается, если ее можно преобразовать в допустимое значение даты и времени. |
| isEnabled |Указывает, включено ли расписание. |
| interval |Тип интервала для расписания.<br><br>day<br>hour |
| frequency |Частота, с которой срабатывает расписание (количество дней или часов). |

Значение времени начала для расписаний должно быть больше, чем значение текущего времени.  Это значение невозможно указать в переменной, так как вы не сможете узнать время установки.

При использовании ресурсов расписания в решении следуйте одной из следующих двух стратегий.

- Используйте параметр для времени запуска в расписании.  Таким образом, пользователю будет предложено ввести значение при установке решения.  Если у вас есть несколько расписаний, для них можно использовать одно значение параметра.
- Создайте расписание с помощью модуля Runbook, который запускается после установки решения.  Таким образом, пользователю не нужно будет указывать время. Однако нельзя добавлять расписание в решение, так как при его удалении расписание тоже будет удалено.


### <a name="job-schedules"></a>Расписания заданий
Ресурсы расписания заданий связывают модуль Runbook с расписанием.  Их тип — **Microsoft.Automation/automationAccounts/jobSchedules**. Их структура приведена ниже.  Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


В следующей таблице описаны свойства расписаний заданий.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| schedule name |Одна сущность **name** с именем расписания. |
| runbook name  |Одна сущность **name** с именем модуля Runbook.  |



## <a name="variables"></a>Переменные
Тип [переменных службы автоматизации Azure](../automation/automation-variables.md) — **Microsoft.Automation/automationAccounts/variables**. Их структура приведена ниже.  Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

В следующей таблице описаны свойства ресурсов переменной.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| description | Необязательное описание переменной. |
| isEncrypted | Указывает, следует ли шифровать переменную. |
| Тип | Сейчас это свойство ни на что не влияет.  Тип данных переменной определяется начальным значением. |
| value | Значение переменной. |

> [!NOTE]
> Сейчас свойство **type** не влияет на создаваемую переменную.  Тип данных переменной определяется значением.  

Если для переменной задается начальное значение, его необходимо настроить в качестве правильного типа данных.  В таблице ниже приведены различные допустимые типы данных и их синтаксис.  Обратите внимание, что значения в JSON всегда должны заключаться в кавычки со всеми специальными знаками.  Например, строковое значение задается с парами кавычек в начале и в конце строки (с escape-символом (\\)), а числовое значение задается с одной парой кавычек.

| Тип данных | ОПИСАНИЕ | Пример | Результат разрешения |
|:--|:--|:--|:--|
| строка   | Заключает значение в две пары кавычек.  | "\"Hello world\"" | "Hello world" |
| numeric  | Числовое значение с одной парой кавычек.| "64" | 64 |
| Логическое  | Значение **true** или **false** в кавычках.  Обратите внимание, что это значение должно быть в нижнем регистре. | True | Да |
| Datetime | Сериализованное значение даты.<br>Вы можете создать это значение для определенной даты с помощью командлета ConvertTo-Json в PowerShell.<br>Пример: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>модули
Решению для управления не нужно определять [глобальные модули](../automation/automation-integration-modules.md), используемые в модулях Runbook, так как они будут доступны постоянно в учетной записи службы автоматизации.  Необходимо включить ресурс для любого модуля, используемого в модулях Runbook.

[Тип модулей интеграции](../automation/automation-integration-modules.md) — **Microsoft.Automation/automationAccounts/modules**. Их структура приведена ниже.  Далее представлены общие переменные и параметры, чтобы этот фрагмент кода можно было скопировать и вставить в файл решения и изменить имена параметров.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


В следующей таблице описаны свойства ресурсов модуля.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| contentLink |Указывает содержимое модуля. <br><br>uri — URI содержимого модуля.  Это будет PS1-файл для модулей Runbook PowerShell и сценариев, а также файл экспортированного графического модуля Runbook для графического модуля Runbook.  <br> version — версия модуля для собственного отслеживания. |

Модуль Runbook должен зависеть от ресурса модуля, чтобы этот ресурс создавался до модуля Runbook.

### <a name="updating-modules"></a>Обновление модулей
Если при обновлении решения для управления, включающего в себя модуль Runbook, который использует расписание, обновляется и модуль, используемый модулем Runbook, то Runbook может вернуться к старой версии модуля.  В решение следует добавить указанные ниже модули Runbook и создать задание для их выполнения перед выполнением остальных модулей Runbook.  Таким образом все модули будут обновляться, как указано, перед загрузкой модулей Runbook.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) обеспечивает то, что модули Runbook в решении используют модули последней версии.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) повторно зарегистрирует все ресурсы расписания, чтобы обеспечить, что модули Runbook, связанные с ними, используют модули последней версии.




## <a name="sample"></a>Образец
Ниже приведен пример решения со приведенными ниже ресурсами.

- Модуль Runbook.  Это пример модуля Runbook, хранящийся в общедоступном репозитории GitHub.
- Задание службы автоматизации, которое запускает модуль Runbook при установке решения.
- Расписание и расписание заданий для запуска модуля Runbook через регулярные интервалы.
- Сертификат.
- Учетные данные.
- Переменная.
- Модуль.  Это [модуль OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) для записи данных в Log Analytics. 

В примере используются переменные [стандартных параметров решения]( monitoring-solutions-solution-file.md#parameters), что является общепринятой практикой для решений, в отличие от жестко программируемых значений в определениях ресурсов.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for shedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Дополнительная информация
* [Добавьте представление в решение]( monitoring-solutions-resources-views.md) для визуализации собранных данных.
