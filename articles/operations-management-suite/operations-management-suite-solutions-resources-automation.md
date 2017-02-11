---
title: "Ресурсы службы автоматизации в решениях OMS | Документация Майкрософт"
description: "Как правило, решения в OMS содержат модули Runbook службы автоматизации Azure для автоматизации процессов, таких как сбор и обработка данных мониторинга.  В этой статье описывается, как добавлять модули Runbook и связанные с ними ресурсы в решение."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>Ресурсы службы автоматизации в решениях OMS (предварительная версия)
> [!NOTE]
> Это предварительная документация для создания решений для управления в консоли OMS, которая доступна в данный момент в режиме предварительной версии. Любые схемы, приведенные ниже, могут измениться.   
> 
> 

Как правило, [решения для управления в OMS](operations-management-suite-solutions.md) содержат модули Runbook службы автоматизации Azure для автоматизации процессов, таких как сбор и обработка данных мониторинга.  Помимо модулей Runbook учетные записи службы автоматизации содержат ресурсы, такие как переменные и расписания, поддерживающие модули Runbook, используемые в решении.  В этой статье описывается, как добавлять модули Runbook и связанные с ними ресурсы в решение.

> [!NOTE]
> В примерах здесь используются обязательные или общие параметры и переменные для решений по управлению, описанные в статье [Создание решений для управления в Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 
> 
> 

## <a name="prerequisites"></a>Предварительные требования
В этой статье предполагается, что вы уже знаете, как [создать решение для управления](operations-management-suite-solutions-creating.md) и структуру файла решения.

## <a name="samples"></a>Примеры
Примеры шаблонов Resource Manager для ресурсов службы автоматизации можно получить из [шаблонов быстрого запуска на GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Учетная запись службы автоматизации
Все ресурсы в службе автоматизации Azure содержатся в [учетной записи службы автоматизации](../automation/automation-security-overview.md#automation-account-overview).  Как описано в разделе [Рабочая область OMS и учетная запись службы автоматизации](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account), учетная запись службы автоматизации не включена в решение для управления и должна быть создана до установки решения.  Если она недоступна, произойдет сбой установки решения.

Имя учетной записи службы автоматизации указывается в имени каждого ресурса службы автоматизации.  Оно указывается в решении с помощью параметра **accountName**, как в следующем примере ресурса модуля Runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Модули Runbook
Тип ресурсов [модуля Runbook службы автоматизации Azure](../automation/automation-runbook-types.md) — **Microsoft.Automation/automationAccounts/runbooks**. Их свойства приведены в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| runbookType |Указывает тип модуля Runbook. <br><br> Script — сценарий PowerShell <br>PowerShell — рабочий процесс PowerShell <br> GraphPowerShell — графический модуль сценария PowerShell <br> GraphPowerShellWorkflow — графический модуль Runbook рабочего процесса PowerShell |
| logProgress |Указывает, нужно ли создавать [записи о ходе выполнения](../automation/automation-runbook-output-and-messages.md) для модуля Runbook. |
| logVerbose |Указывает, нужно ли создавать [подробные записи](../automation/automation-runbook-output-and-messages.md) для модуля Runbook. |
| Описание |Необязательное описание модуля Runbook. |
| publishContentLink |Указывает содержимое модуля Runbook. <br><br>uri — URI содержимого модуля Runbook.  Это будет PS1-файл для модулей Runbook PowerShell и сценариев, а также файл экспортированного графического модуля Runbook для графического модуля Runbook.  <br> version — версия модуля Runbook для собственного отслеживания. |

Ниже приведен пример ресурса модуля Runbook.  В этом случае он извлекает модуль Runbook из [коллекции PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Запуск модуля Runbook
В решении для управления модуль Runbook можно запустить двумя способами.

* Для запуска модуля Runbook при установке решения создайте [ресурс задания](#automation-jobs), как описано ниже.
* Для запуска модуля Runbook по расписанию создайте [ресурс расписания](#schedules), как описано ниже. 

## <a name="automation-jobs"></a>Задания службы автоматизации
Для запуска модуля Runbook при установке решения для управления следует создать ресурс **задания**.  Тип ресурсов задания — **Microsoft.Automation/automationAccounts/jobs**. Их свойства приведены в таблице ниже.

| Свойство | Description (Описание) |
|:--- |:--- |
| runbook |Одна сущность **name** с именем модуля Runbook. |
| parameters |Сущность для каждого параметра, необходимого для модуля Runbook. |

Задание включает в себя имя модуля Runbook и все значения параметров, которые нужно отправить в модуль Runbook.  Задание должно [зависеть от](operations-management-suite-solutions-creating.md#resources) модуля Runbook, который оно запускает, так как модуль Runbook нужно создать до задания.  Кроме того, для модулей Runbook нужно создать зависимости от других заданий, которые должны завершиться до текущего задания.

Имя ресурса задания должно содержать GUID, который обычно назначается с помощью параметра.  Дополнительные сведения о параметрах GUID см. в статье [Создание решений для управления в Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Ниже представлен пример задания, которое запускает модуль Runbook при установке решения для управления.  Другие модули Runbook должны завершиться до начала выполнения текущего, чтобы в нем были зависимости от заданий для этих модулей Runbook.  Сведения о задании указываются в параметрах и переменных, а не напрямую.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Сертификаты
Тип [сертификатов службы автоматизации Azure](../automation/automation-certificates.md) — **Microsoft.Automation/automationAccounts/certificates**. Их свойства приведены в таблице ниже.

| Свойство | Описание |
|:--- |:--- |
| base64Value |Значение Base 64 для сертификата. |
| thumbprint |Отпечаток сертификата. |

Ниже указан пример ресурса сертификата.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Учетные данные
Тип [учетных данных службы автоматизации Azure](../automation/automation-credentials.md) — **Microsoft.Automation/automationAccounts/credentials**. Их свойства приведены в таблице ниже.

| Свойство | Description (Описание) |
|:--- |:--- |
| userName |Имя пользователя для учетных данных. |
| password |Пароль для учетных данных. |

Ниже указан пример ресурса учетных данных.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Расписания
Тип [расписаний службы автоматизации Azure](../automation/automation-schedules.md) — **Microsoft.Automation/automationAccounts/schedules**. Их свойства приведены в таблице ниже.

| Свойство | Description (Описание) |
|:--- |:--- |
| Описание |Необязательное описание расписания. |
| startTime |Указывает время начала расписания в виде объекта DateTime. Строка указывается, если ее можно преобразовать в допустимое значение даты и времени. |
| isEnabled |Указывает, включено ли расписание. |
| interval |Тип интервала для расписания.<br><br>day<br>hour |
| frequency |Частота, с которой срабатывает расписание (количество дней или часов). |

Ниже указан пример ресурса расписания.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Переменные
Тип [переменных службы автоматизации Azure](../automation/automation-variables.md) — **Microsoft.Automation/automationAccounts/variables**. Их свойства приведены в таблице ниже.

| Свойство | Description (Описание) |
|:--- |:--- |
| Описание |Необязательное описание переменной. |
| isEncrypted |Указывает, следует ли шифровать переменную. |
| type |Тип данных для переменной. |
| value |Значение переменной. |

Ниже указан пример ресурса переменной.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>модули
Решению для управления не нужно определять [глобальные модули](../automation/automation-integration-modules.md), используемые в модулях Runbook, так как они будут доступны постоянно.  Однако необходимо включить ресурс для всех остальных модулей, используемых в модулях Runbook. Модуль Runbook должен зависеть от ресурса модуля, чтобы обеспечить создание ресурса до модуля Runbook.

[Тип модулей интеграции](../automation/automation-integration-modules.md) — **Microsoft.Automation/automationAccounts/modules**. Их свойства приведены в таблице ниже.

| Свойство | Description (Описание) |
|:--- |:--- |
| contentLink |Указывает содержимое модуля. <br><br>uri — URI содержимого модуля Runbook.  Это будет PS1-файл для модулей Runbook PowerShell и сценариев, а также файл экспортированного графического модуля Runbook для графического модуля Runbook.  <br> version — версия модуля Runbook для собственного отслеживания. |

Ниже указан пример ресурса модуля.

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Обновление модулей
Если при обновлении решения для управления, включающего в себя модуль Runbook, который использует расписание, обновляется и модуль, используемый модулем Runbook, то Runbook может вернуться к старой версии модуля.  В решение следует добавить указанные ниже модули Runbook и создать задание для их выполнения перед выполнением остальных модулей Runbook.  Таким образом все модули будут обновляться, как указано, перед загрузкой модулей Runbook.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) обеспечивает то, что модули Runbook в решении используют модули последней версии.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) повторно зарегистрирует все ресурсы расписания, чтобы обеспечить, что модули Runbook, связанные с ними, используют модули последней версии.

Ниже приведен пример обязательных элементов решения для поддержки обновления модуля.

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Дальнейшие действия
* [Добавьте представление в решение](operations-management-suite-solutions-resources-views.md) для визуализации собранных данных.




<!--HONumber=Nov16_HO3-->


