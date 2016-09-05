<properties
	pageTitle="Операции аудита с помощью диспетчера ресурсов | Microsoft Azure"
	description="Просмотр действий пользователя и ошибок с помощью журнала действий в Resource Manager. Отображаются портал Azure, PowerShell, интерфейс командной строки Azure и REST."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="tomfitz"/>

# Операции аудита с помощью диспетчера ресурсов

С помощью журналов действий можно определить:

- какие операции выполнялись с ресурсами в вашей подписке;
- кто инициировал операцию (при этом для операций, инициированных серверной службой, имя вызывающего пользователя не возвращается);
- когда была выполнена операция;
- состояние операции;
- значения других свойств, которые могут помочь в изучении операции.

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Этот раздел посвящен аудиту операций. Сведения об устранении неполадок развертывания с помощью журналов действий см. в статье [Просмотр операций развертывания с помощью портала Azure](resource-manager-troubleshoot-deployments-portal.md).

Сведения из журналов действий можно получить с помощью портала, PowerShell, интерфейса командной строки Azure, API REST Insights или с помощью [библиотеки .NET для Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Просмотр журналов действий на портале

1. Чтобы просмотреть журналы действий на портале, выберите пункт **Другие службы**, а затем — **Журналы действий**.

    ![просмотр журналов действий](./media/resource-group-audit/select-audit-logs.png)

2. В колонке **Журнал действий** приводится сводка последних операций для всех групп ресурсов в вашей подписке. Среди прочего здесь есть список недавних операций.

    ![отображение действий](./media/resource-group-audit/audit-summary.png)

3. Чтобы ограничить количество отображаемых операций, выберите различные условия. Например, на следующем рисунке показано, как изменение полей **Временной диапазон** и **Кем инициировано событие** позволяет просмотреть действия конкретного пользователя или приложения за прошлый месяц.

    ![установка параметров фильтра](./media/resource-group-audit/set-filter.png)

4. Выберите **Применить** для просмотра результатов запроса.

5. Если вы хотите повторить тот же запрос позже, выберите **Сохранить** и укажите имя запроса.

    ![сохранение запроса](./media/resource-group-audit/save-query.png)

6. Если вы перейдете к **журналу действий** из колонки ресурса, журнал будет автоматически отфильтрован по этому ресурсу или группе ресурсов. Обратите внимание, что журнал действий автоматически фильтруется по выбранному ресурсу.

    ![фильтрация по ресурсам](./media/resource-group-audit/filtered-by-resource.png)

## Просмотр журналов действий с помощью PowerShell

1. Чтобы получить записи журнала, выполните команду **Get-AzureRmLog**. Укажите дополнительные параметры, чтобы отфильтровать список записей. Если не указать время начала и окончания, возвращаются записи за последний час. Например, для получения операций для группы ресурсов за последний час выполните следующую команду:

        Get-AzureRmLog -ResourceGroup ExampleGroup

    В следующем примере показано, как использовать журнал аудита для анализа действий, выполненных в течение указанного времени. Даты начала и окончания указывайте в формате даты.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

    Диапазон дат, например последние 14 дней, также можно указать с помощью функций даты.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)

2. В зависимости от указанного времени начала приведенные выше команды могут вернуть длинный список операций для группы ресурсов. Чтобы найти в результатах нужную информацию, отфильтруйте их, используя условия поиска. Например, чтобы проанализировать обстоятельства остановки веб-приложения, выполните приведенную ниже команду.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
        
    В нашем примере вы видите, что веб-приложение было остановлено пользователем someone@contoso.com.
        
        Authorization     :
        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Action    : Microsoft.Web/sites/stop/action
        Role      : Subscription Admin
        Condition :
        Caller            : someone@contoso.com
        CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
        EventSource       : Administrative
        EventTimestamp    : 8/28/2015 4:08:18 PM
        OperationName     : Microsoft.Web/sites/stop/action
        ResourceGroupName : ExampleGroup
        ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
        Status            : Succeeded
        SubscriptionId    : xxxxx
        SubStatus         : OK

3. Можно найти действия, выполненные конкретным пользователем, даже для группы ресурсов, которой больше не существует.

        Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## Просмотр журналов действий с помощью интерфейса командной строки Azure

1. Чтобы получить записи журнала, выполните команду **azure group log show**.

        azure group log show ExampleGroup

2. Вы можете отфильтровать результаты с помощью служебной программы JSON, например [jq](http://stedolan.github.io/jq/download/). В следующем примере показано, как найти операции, связанные с обновлением файла веб-конфигурации.

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. Вы можете найти действия для конкретного пользователя.

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## Просмотр журналов аудита с помощью API REST

Операции REST для работы с журналом действий включены в интерфейс [REST API Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Получение событий журнала действий описано в статье [Список событий управления в подписке](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Дальнейшие действия

- Чтобы получить больше информации о действиях в вашей подписке, можно использовать журналы аудита Azure совместно с Power BI. Дополнительные сведения см. в записи блога [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Журналы аудита Azure в Power BI: просмотр, анализ и другие возможности).
- Дополнительные сведения о настройке политик безопасности см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).
- Сведения о командах для устранения неполадок при развертывании см. в статье [Просмотр операций развертывания с помощью портала Azure](resource-manager-troubleshoot-deployments-portal.md).
- Вы можете запретить всем пользователям операции удаления для определенного ресурса, как описано в статье [Блокировка ресурсов с помощью Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0824_2016-->