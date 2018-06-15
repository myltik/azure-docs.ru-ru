---
title: Сбор данных для Log Analytics с использованием модуля runbook в службе автоматизации Azure | Документация Майкрософт
description: Пошаговое руководство по созданию модуля runbook в службе автоматизации Azure для сбора данных в репозитории OMS и анализа с помощью Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 0784e2317fbc98561b486547654ca27bb30e76c3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597373"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Сбор данных в Log Analytics с использованием модуля runbook в службе автоматизации Azure
В Log Analytics можно собрать значительный объем данных из различных источников, включая [источники данных](../log-analytics/log-analytics-data-sources.md) в агентах и [данные, собранные в Azure](../log-analytics/log-analytics-azure-storage.md).  Но иногда требуется собирать данные, недоступные в этих стандартных источниках.  В таких случаях вы можете использовать [API сборщика данных HTTP](../log-analytics/log-analytics-data-collector-api.md), чтобы записать данные в Log Analytics из любого клиента REST API.  Чаще всего такие данные собираются с помощью модулей runbook в службе автоматизации Azure.   

В этом руководстве описан пошаговый процесс создания модуля runbook и расписания для него в службе автоматизации Azure, позволяющий записывать данные в Log Analytics.


## <a name="prerequisites"></a>предварительным требованиям
Для выполнения этого сценария нужно настроить в подписке Azure указанные ниже ресурсы.  Их можно использовать с бесплатной учетной записью.

- [Рабочая область Log Analytics](../log-analytics/log-analytics-get-started.md).
- [Учетная запись службы автоматизации Azure](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Обзор сценария
В этом руководстве вы создадите модуль runbook, который собирает сведения о заданиях службы автоматизации.  Модули runbook в службе автоматизации Azure реализуются с помощью PowerShell, поэтому начнем с написания и тестирования скрипта в редакторе службы автоматизации Azure.  Убедившись, что вы собрали необходимые сведения, запишите эти данные в Log Analytics и проверьте тип пользовательских данных.  В завершение создайте расписание для запуска модуля runbook через регулярные интервалы.

> [!NOTE]
> Службу автоматизации Azure можно настроить таким образом, чтобы сведения о задании отправлялись в Log Analytics без этого модуля runbook.  Этот сценарий в основном используется для работы с руководством. Рекомендуем отправлять данные в тестовую рабочую область.  


## <a name="1-install-data-collector-api-module"></a>1. Установка модуля API сборщика данных
Каждый [запрос от API сборщика данных HTTP](../log-analytics/log-analytics-data-collector-api.md#create-a-request) должен быть правильно отформатирован и содержать заголовок авторизации.  Это можно сделать в модуле runbook, но вы можете уменьшить объем требуемого кода с помощью модуля, упрощающего процесс.  Один из модулей, которые можно использовать, — это [модуль OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI) в коллекции PowerShell.

Чтобы использовать [модуль](../automation/automation-integration-modules.md) в runbook, установите его в своей учетной записи службы автоматизации.  После этого любой модуль runbook в той же учетной записи сможет использовать функции из модуля.  Вы можете установить новый модуль, последовательно выбрав в своей учетной записи службы автоматизации **Ресурсы** > **Модули** > **Добавить модуль**.  

Кроме того, коллекция PowerShell позволяет быстро развернуть модуль непосредственно в учетной записи службы автоматизации. Воспользуемся этой возможностью для работы с нашим руководством.  

![Модуль OMSIngestionAPI](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Перейдите к [коллекции PowerShell](https://www.powershellgallery.com/).
2. Введите в строке поиска **OMSIngestionAPI**.
3. Нажмите кнопку **Deploy to Azure Automation** (Развертывание в службе автоматизации Azure).
4. Выберите учетную запись службы автоматизации и нажмите кнопку **ОК**, чтобы установить модуль.


## <a name="2-create-automation-variables"></a>2. Создание переменных службы автоматизации
[Переменные службы автоматизации](..\automation\automation-variables.md) содержат значения, которые могут использоваться всеми модулями runbook в учетной записи службы автоматизации.  Вы можете изменить эти значения, не изменяя сам модуль runbook, чтобы повысить его гибкость. Для каждого запроса API сборщика данных HTTP требуется идентификатор и ключ рабочей области OMS, а ресурсы переменных отлично подходят для хранения таких данных.  

![Переменные](media/operations-management-suite-runbook-datacollect/variables.png)

1. На портале Azure перейдите к учетной записи службы автоматизации.
2. Выберите **Переменные** в разделе **Общие ресурсы**.
2. Щелкните **Добавить переменную** и создайте две переменные в таблице ниже.

| Свойство | Значение идентификатора рабочей области | Значение ключа рабочей области |
|:--|:--|:--|
| ИМЯ | WorkspaceId | WorkspaceKey |
| type | Строка | Строка |
| Значение | Вставьте идентификатор рабочей области Log Analytics. | Вставьте первичный или вторичный ключ рабочей области Log Analytics. |
| зашифрованные; | Нет  | Yes |



## <a name="3-create-runbook"></a>3. Создание модуля Runbook

На портале для службы автоматизации Azure предусмотрен редактор, с помощью которого можно изменять и тестировать модуль runbook.  Вы можете использовать редактор скриптов для [работы непосредственно с PowerShell](../automation/automation-edit-textual-runbook.md) или [создать графический модуль runbook](../automation/automation-graphical-authoring-intro.md).  В этом руководстве мы будем работать со скриптом PowerShell. 

![Изменение модуля runbook](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Перейдите к учетной записи службы автоматизации.  
2. Щелкните **Модули Runbook** > **Добавить Runbook** > **Создание нового модуля Runbook**.
3. В качестве имени модуля runbook введите **Collect-Automation-jobs**.  Для типа runbook выберите значение **PowerShell**.
4. Щелкните **Создать**, чтобы создать модуль runbook и запустить текстовый редактор.
5. Скопируйте приведенный ниже код и вставьте его в модуль runbook.  См. комментарии в скрипте для объяснения кода.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Тестирование модуля runbook
Служба автоматизации Azure содержит среду для [тестирования модуля runbook](../automation/automation-testing-runbook.md) перед публикацией.  Можно проверить данные, собранные модулем runbook, и убедиться, что он надлежащим образом записывает их в Log Analytics перед публикацией в производственной среде. 
 
![Тестирование модуля runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Нажмите кнопку **Сохранить**, чтобы сохранить модуль runbook.
1. Щелкните **Область тестирования**, чтобы открыть runbook в тестовой среде.
3. Когда в модуле runbook станут доступны параметры, для них будет предложено ввести значения.  Введите имя группы ресурсов и учетной записи службы автоматизации, из которых вы будете собирать сведения о задании.
4. Щелкните **Пуск**, чтобы запустить модуль runbook.
3. Модуль runbook запустится с состоянием **В очереди**, а затем перейдет в состояние **Выполняется**.  
3. Для модуля runbook должны отображаться подробные выходные данные с собранными заданиями в формате JSON.  Если в списке нет заданий, возможно, за последний час в учетной записи службы автоматизации задания не создавались.  Попробуйте запустить любой модуль runbook в учетной записи службы автоматизации и выполнить тестирование еще раз.
4. Убедитесь, что в выходных данных не отображаются сообщения об ошибках команды POST для Log Analytics.  Должно появиться примерно такое сообщение:

    ![Выходные данные POST](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Проверка записей в Log Analytics
Когда тестирование runbook будет завершено и вы убедитесь, что выходные данные получены успешно, можно проверить, созданы ли записи, с помощью [поиска по журналам в Log Analytics](../log-analytics/log-analytics-log-searches.md).

![Выходные данные журналов](media/operations-management-suite-runbook-datacollect/log-output.png)

1. На портале Azure выберите рабочую область Log Analytics.
2. Щелкните **Поиск по журналам**.
3. Введите команду `Type=AutomationJob_CL` и нажмите кнопку поиска. Обратите внимание, что тип записи включает суффикс _CL, не указанный в скрипте.  Этот суффикс автоматически добавляется в тип журнала, чтобы указать, что журнал пользовательский.
4. Должна отобразиться одна или несколько возвращенных записей. Это указывает на то, что модуль runbook работает правильно.


## <a name="6-publish-the-runbook"></a>6. Публикация модуля runbook
Убедившись, что модуль runbook работает правильно, необходимо опубликовать его, чтобы запустить в рабочей среде.  Вы можете и дальше изменять и тестировать модуль runbook без изменения опубликованной версии.  

![Публикация модуля runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Вернитесь к учетной записи службы автоматизации.
2. Щелкните **Модули Runbook** и выберите **Collect-Automation-jobs**.
3. Щелкните **Правка** и выберите **Опубликовать**.
4. Нажмите **Да** при запросе на подтверждение перезаписи для ранее опубликованной версии.

## <a name="7-set-logging-options"></a>7. Настройка параметров ведения журнала 
Для тестирования можно было просмотреть [подробные выходные данные](../automation/automation-runbook-output-and-messages.md#message-streams), так как в скрипте была задана переменная $VerbosePreference.  Если вы хотите просмотреть подробные выходные данные в рабочей среде, задайте для модуля runbook свойства ведения журнала.  Для модуля runbook, используемого в этом руководстве, отобразятся данные JSON, отправляемые в Log Analytics.

![Ведение журнала и трассировка](media/operations-management-suite-runbook-datacollect/logging.png)

1. В окне свойств для модуля runbook выберите **Ведение журнала и трассировка** в разделе **Параметры Runbook**.
2. Задайте для параметра **Подробные записи в журнале** значение **Вкл.**
3. Выберите команду **Сохранить**.

## <a name="8-schedule-runbook"></a>8. Создание расписания для модуля runbook
Наиболее распространенным способом запуска модуля runbook, который собирает данные мониторинга, является автоматический запуск по расписанию.  Для этого создайте [расписание в службе автоматизации Azure](../automation/automation-schedules.md) и подключите его к модулю runbook.

![Создание расписания для модуля runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. В окне свойств модуля runbook выберите **Расписания** в разделе **Ресурсы**.
2. Последовательно выберите **Добавить расписание** > **Связать расписание с модулем Runbook** > **Создать новое расписание**.
5. Введите указанные ниже значения для расписания и нажмите кнопку **Создать**.

| Свойство | Значение |
|:--|:--|
| ИМЯ | AutomationJobs-Hourly |
| Запуск | Выберите любое время как минимум на 5 минут позже текущего. |
| Периодичность | Повторение |
| Повторять каждые | 1 час |
| Срок действия | Нет  |

Создав расписание, задайте значения параметров, которые будут использоваться при каждом запуске модуля runbook по этому расписанию.

6. Щелкните **Настройка параметров и настроек запуска**.
7. Укажите значения для **ResourceGroupName** и **AutomationAccountName**.
8. Последовательно выберите **ОК**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Проверка запуска модуля runbook по расписанию
При каждом запуске модуля runbook [создается задание](../automation/automation-runbook-execution.md), и все выходные данные записываются в журнал.  Фактически это те же задания, которые собирает модуль runbook.  Вы можете убедиться, что модуль runbook запускается правильно, проверив задания для него после момента запуска по расписанию.

![Задания](media/operations-management-suite-runbook-datacollect/jobs.png)

1. В окне свойств модуля runbook выберите **Задания** в разделе **Ресурсы**.
2. Для каждого запуска модуля runbook должен отображаться список заданий.
3. Щелкните одно из заданий, чтобы просмотреть сведения о нем.
4. Щелкните **Все журналы**, чтобы просмотреть журналы и выходные данные модуля runbook.
5. Прокрутите вниз, чтобы найти запись, как на изображении ниже.<br>![Подробная информация](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Щелкните эту запись для просмотра подробных данных JSON, отправленных в Log Analytics.



## <a name="next-steps"></a>Дополнительная информация
- При помощи [конструктора представлений](../log-analytics/log-analytics-view-designer.md) создайте представление с данными, собранными в репозитории Log Analytics.
- Поместите модуль runbook в пакет [решения по управлению](operations-management-suite-solutions-creating.md), чтобы можно было распространять его среди заказчиков.
- Дополнительные сведения о [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Дополнительные сведения о [службе автоматизации Azure](https://docs.microsoft.com/azure/automation/).
- Дополнительные сведения об [API сборщика данных HTTP](../log-analytics/log-analytics-data-collector-api.md).
