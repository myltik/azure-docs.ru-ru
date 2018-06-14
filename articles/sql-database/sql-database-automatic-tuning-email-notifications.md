---
title: Практическое руководство по уведомлениям по электронной почте об автоматической настройке для службы "База данных SQL Azure" | Документация Майкрософт
description: База данных SQL анализирует SQL-запрос и автоматически подстраивается под рабочую нагрузку пользователей.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: 643740ea76769f857e8c99ebaa6d27eceed99067
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644337"
---
# <a name="email-notifications-for-automatic-tuning"></a>Уведомления по электронной почте об автоматической настройке

Рекомендации по настройке базы данных SQL создает функция [автоматической настройки](sql-database-automatic-tuning.md) службы "База данных SQL Azure". Это решение постоянно отслеживает и анализирует рабочие нагрузки баз данных SQL, предоставляя для каждой отдельной базы данных специализированные рекомендации по настройке, связанные с созданием индексов, удалением индексов и оптимизацией планов выполнения запросов.

Рекомендации по автоматической настройке базы данных SQL можно просмотреть на [портале Azure](sql-database-advisor-portal.md), а также получить с помощью вызовов [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) либо с помощью команд [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) и [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction). Эта статья основана на использовании сценария PowerShell для получения рекомендаций по автоматической настройке.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Автоматизация уведомлений по электронной почте о рекомендациях по автоматической настройке

Приведенное ниже решение автоматизирует отправку уведомлений по электронной почте, содержащих рекомендации по автоматической настройке. Описываемое решение включает в себя автоматизацию выполнения сценария PowerShell для получения рекомендаций по настройке с помощью [службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro) и автоматизацию планирования задания доставки электронных сообщений с помощью [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure

Чтобы использовать службу автоматизации Azure, сначала следует создать учетную запись службы автоматизации и настроить для нее ресурсы Azure, чтобы выполнять сценарий PowerShell. Дополнительные сведения об использовании службы автоматизации Azure см. в статье [Начало работы со службой автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Выполните следующие действия, чтобы создать учетную запись службы автоматизации Azure, выбрав и настроив приложение Automation из Marketplace.

- Войдите на портал Azure.
- Щелкните **+ Создать ресурс** в верхнем левом углу.
- Введите в строку поиска **Automation** (нажмите клавишу "ВВОД").
- Щелкните приложение Automation в результатах поиска.

![Добавление службы автоматизации Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- В области "Создание учетной записи автоматизации" щелкните **Создать**.
- Укажите необходимые сведения: введите имя этой учетной записи службы автоматизации, выберите идентификатор подписки Azure и ресурсы Azure для выполнения сценария PowerShell.
- Для параметра **Создать учетную запись запуска от имени Azure** выберите **Да**, чтобы настроить тип учетной записи, с которой сценарий PowerShell будет выполняться с помощью службы автоматизации Azure. Чтобы узнать больше о типах учетных записей, прочитайте раздел [Обновление проверки подлинности учетных записей службы автоматизации с использованием учетных записей запуска от имени](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
- Завершите создание учетной записи службы автоматизации, щелкнув **Создать**.

> [!TIP]
> Запишите имя учетной записи службы автоматизации Azure, идентификатор подписки и ресурсы (например, скопировав и вставив их в Блокнот), введенные при создании приложения Automation. Эти сведения потребуются вам позже.
>

Если у вас несколько подписок Azure, для которых вы хотите использовать одинаковые параметры автоматизации, необходимо повторить эту процедуру для других подписок.

## <a name="update-azure-automation-modules"></a>Обновление модулей службы автоматизации Azure

Для получения рекомендаций по автоматической настройке сценарий PowerShell использует команды [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) и [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction), требующие обновления модулей Azure до версии 4 и более поздней версии.

Вот как можно обновить модули Azure PowerShell.

- Перейдите в область приложения Automation и выберите **Модули** в меню слева (прокрутите страницу вниз, так как этот пункт меню расположен в разделе "Общие ресурсы").
- Вверху области "Модули" щелкните **Обновить модули Azure** и подождите, пока не появится сообщение "Модули Azure обновлены". Процесс создания может занять несколько минут.

![Обновление модулей службы автоматизации Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Необходимо использовать модули AzureRM.Resources и AzureRM.Sql версии 4 и более поздней версии.

## <a name="create-azure-automation-runbook"></a>Создание runbook службы автоматизации Azure

Следующим шагом является создание модуля runbook в службе автоматизации Azure, содержащего сценарий PowerShell для получения рекомендаций по настройке.

Выполните приведенные ниже действия, чтобы создать runbook службы автоматизации Azure

- Перейдите к учетной записи службы автоматизации Azure, созданной на предыдущем шаге.
- В области этой учетной записи службы автоматизации щелкните пункт меню **Модули Runbook** слева, чтобы создать runbook службы автоматизации Azure со сценарием PowerShell. Чтобы узнать больше о создании модулей runbook службы автоматизации, ознакомьтесь с разделом [Создание или импорт модуля Runbook в службе автоматизации Azure](../automation/automation-creating-importing-runbook.md).
- Чтобы добавить новый runbook, щелкните пункт меню **+ Добавить Runbook**, а затем щелкните **Быстрое создание — создать новый Runbook**.
- В области Runbook введите имя runbook (в данном примере это **AutomaticTuningEmailAutomation**), выберите тип runbook — **PowerShell**, а затем введите описание этого runbook, чтобы пояснить его назначение.
- Нажмите кнопку **Создать**, чтобы завершить создание runbook.

![Добавление runbook службы автоматизации Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Выполните следующие действия, чтобы загрузить сценарий PowerShell в созданный runbook.

- В области **Изменение Runbook PowerShell** выберите **Модули Runbook** в дереве меню, а затем разворачивайте представление, пока не увидите имя своего runbook (в этом примере —  **AutomaticTuningEmailAutomation**). Выберите этот runbook.
- В первой строке области "Изменение PowerShell Runbook" (начинающейся с цифры 1) вставьте приведенный ниже код сценария PowerShell, предварительно скопировав его в буфер обмена. Этот сценарий PowerShell предоставляется "как есть" и дает возможность приступить к работе. Измените его в соответствии со своими потребностями.

В заголовке предоставленного сценария PowerShell необходимо заменить `<SUBSCRIPTION_ID_WITH_DATABASES>` своим идентификатором подписки Azure. Чтобы узнать, как получить идентификатор подписки Azure, прочитайте раздел [Getting your Azure Subscription GUID (new portal)](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/) (Получение GUID подписки Azure на новом портале).

В случае, если используется несколько подписок, их можно добавить в свойство $subscriptions в заголовке сценария, разделив запятыми.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Нажмите кнопку **Сохранить** в правом верхнем углу, чтобы сохранить сценарий. Когда сценарий будет готов, нажмите кнопку **Опубликовать**, чтобы опубликовать этот runbook. 

В основной области runbook можно нажать кнопку **Запустить**, чтобы **протестировать** сценарий. Щелкните **Выходные данные** для просмотра результатов выполнения сценария. Эти выходные данные будут отображаться в получаемых электронных сообщениях. На следующем снимке экрана можно увидеть пример выходных данных сценария.

![Представление рекомендаций по автоматической настройке с помощью службы автоматизации Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Обязательно измените содержимое, настроив сценарий PowerShell в соответствии со своими потребностями.

После выполнения указанных выше шагов сценарий PowerShell для получения рекомендаций по автоматической настройке загружается в службу автоматизации Azure. Следующим шагом является автоматизация и планирование задания доставки электронных сообщений.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Автоматизация заданий доставки электронных сообщений с помощью Microsoft Flow

Для завершения решения последним шагом является создание потока автоматизации в Microsoft Flow, состоящего из трех действий (заданий). 

1. **Azure Automation - Create job** (Служба автоматизации Azure — создание задания). Используется для выполнения сценария PowerShell для получения рекомендаций по автоматической настройке, содержащегося в runbook службы автоматизации Azure.
2. **Azure Automation - Get job output** (Служба автоматизации Azure — получение выходных данных задания). Используется для получения выходных данных выполненного сценария PowerShell.
3. **Office 365 Outlook – Send an email** (Outlook Office 365 — отправка электронных сообщений). Используется для отправки электронного сообщения. Электронные сообщения отправляются с помощью учетной записи Office 365 пользователя, создавшего поток.

Чтобы узнать больше о возможностях Microsoft Flow, прочитайте статью [Начало работы с Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Необходимым условием для выполнения этого шага является регистрация для получения учетной записи [Microsoft Flow](https://flow.microsoft.com) и выполнение входа с ее помощью. Перейдя в это решение, выполните следующие действия, чтобы настроить **новый поток**.

- Перейдите к пункту меню **Мои потоки**.
- Вверху страницы "Мои потоки" выберите ссылку **+ Создать с нуля**.
- Щелкните ссылку **Search for hundreds of connectors and triggers** (Поиск сотен соединителей и триггеров) в нижней части страницы.
- В поле поиска введите **повторение** и выберите **Расписание — повторение** в результатах поиска, чтобы запланировать запуск задания доставки электронных сообщений.
- В области "Повторение" в поле "Частота" выберите частоту планирования для выполнения этого потока. Например, можно настроить отправку автоматизированных электронных сообщений каждую минуту, час, день, неделю и т. д.

Следующим шагом является добавление в только что созданный повторяющийся поток трех заданий (создание, получение выходных данных и отправка электронного сообщения). Чтобы добавить необходимые задания в поток, выполните следующие действия.

1. Создайте действие для выполнения сценария PowerShell, который получает рекомендации по настройке.
- Выберите **+ Новый шаг**, затем — **добавить действие** в области повторяющегося потока.
- В поле поиска введите **automation** и выберите **Azure Automation – Create job** (Служба автоматизации Azure — создание задания) в результатах поиска.
- В области "Создание задания" настройте свойства задания. Для этой конфигурации потребуются сведения об идентификаторе подписки Azure, группе ресурсов и учетной записи службы автоматизации, **сохраненные ранее** в **области учетной записи службы автоматизации**. Чтобы узнать больше о параметрах, доступных в этом разделе, изучите раздел [Служба автоматизации Azure](https://docs.microsoft.com/connectors/azureautomation/#Create_job).
- Завершите создание этого действия, щелкнув **Сохранить поток**.

2. Создание действия для получения выходных данных выполненного сценария PowerShell
- Выберите **+ Новый шаг**, затем — **добавить действие** в области повторяющегося потока.
- В поле поиска введите **automation** и выберите **Azure Automation – Get job output** (Служба автоматизации Azure — получение выходных данных задания) в результатах поиска. Чтобы узнать больше о параметрах, доступных в этом разделе, изучите раздел [Служба автоматизации Azure](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output).
- Заполните обязательные поля (как при создании предыдущего задания). Укажите идентификатор подписки Azure, группу ресурсов и учетную запись службы автоматизации (введенную в области учетной записи службы автоматизации).
- Щелкните в поле **Идентификатор задания**, чтобы отобразить меню **Динамическое содержимое**. В это меню выберите параметр **Идентификатор задания**.
- Завершите создание этого действия, щелкнув **Сохранить поток**.

3. Создание действия для отправки электронных сообщений с помощью интеграции Office 365
- Выберите **+ Новый шаг**, затем — **добавить действие** в области повторяющегося потока.
- В поле поиска введите **send an email** и выберите **Office 365 Outlook – Send an email** (Outlook Office 365 — отправка электронных сообщений) в результатах поиска.
- В поле **Кому** введите адрес электронной почты, на который необходимо отправлять уведомления по электронной почте.
- В поле **Тема** введите тему электронного сообщения, например "Уведомление о рекомендациях по автоматической настройке".
- Щелкните в поле **Текст**, чтобы отобразить меню **Динамическое содержимое**. В этом меню в разделе **Get job output** (Получение выходных данных задания) выберите **Содержимое**. 
- Завершите создание этого действия, щелкнув **Сохранить поток**.

> [!TIP]
> Чтобы отправлять автоматизированные электронные сообщения другим получателям, создайте отдельные потоки. В этих дополнительных потоках измените адрес электронной почты получателя в поле "Кому", а также строку темы электронного сообщения в поле "Тема". Создание в службе автоматизации Azure модулей runbook с пользовательскими сценариями PowerShell (например, для изменения идентификатора подписки Azure) обеспечивает дальнейшую настройку автоматизированных сценариев. Например, можно настроить отправку по электронной почте отдельным получателям рекомендаций по автоматической настройке для отдельных подписок.
>

На этом действия, необходимые для настройки рабочего процесса задания доставки электронных сообщений, завершены. На следующем рисунке показан весь поток, состоящий из трех действий.

![Представление потока уведомлений по электронной почте об автоматической настройке](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Чтобы проверить поток, щелкните **Выполнить** в правом верхнем углу области потока.

Статистику выполнения автоматизированных заданий, отображающую успешность отправки уведомлений по электронной почте, можно просмотреть в области "Аналитика потока".

![Выполнение потока уведомлений по электронной почте об автоматической настройке](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Аналитика потока полезна для наблюдения за успешностью выполнения задания и устранения неполадок, когда это необходимо.  В случае устранение неполадок также может потребоваться просмотреть журнал выполнения сценария PowerShell, доступный в приложении службы автоматизации Azure.

Конечные выходные данные автоматизированных электронных сообщений аналогичны приведенному ниже электронному сообщению, полученному после выполнения сборки и запуска этого решения.

![Пример выходных данных уведомлений по электронной почте об автоматической настройке](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Изменяя параметры сценария PowerShell, можно настроить выходные данные и форматирование автоматизированных электронных сообщений в соответствии со своими потребностями.

Можно дополнительно настроить решение для создания уведомлений по электронной почте на основании определенного события настройки, отправляемых нескольким получателям, для нескольких подписок или баз данных. Это может зависеть от ваших конкретных сценариев. 

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о том, как автоматическая настройка может помочь повысить производительность базы данных, ознакомившись с разделом [Автоматическая настройка в базе данных SQL Azure](sql-database-automatic-tuning.md).
- Дополнительные сведения о том, как включить автоматическую настройку в базе данных SQL Azure, чтобы управлять рабочей нагрузкой, см. в статье [Включение автоматической настройки](sql-database-automatic-tuning-enable.md).
- Дополнительные сведения о том, как вручную проверить и применить рекомендации автоматической настройки, см. в статье [Поиск и применение рекомендаций по производительности](sql-database-advisor-portal.md).
