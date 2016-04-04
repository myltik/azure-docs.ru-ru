<properties
	pageTitle="Операции аудита с помощью диспетчера ресурсов | Microsoft Azure"
	description="Просмотр действий пользователя и ошибок с помощью журнала аудита в диспетчере ресурсов. Отображаются портал Azure, PowerShell, интерфейс командной строки Azure и REST."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tomfitz"/>

# Операции аудита с помощью диспетчера ресурсов

С помощью журналов аудита можно определить:

- какие операции были выполнены для ресурсов в вашей подписке;
- кто инициировал операцию (при этом для операций, инициированных серверной службой, имя вызывающего пользователя не возвращается);
- когда была выполнена операция;
- состояние операции;
- значения других свойств, которые могут помочь в изучении операции.

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Этот раздел посвящен аудиту операций. Сведения об устранении неполадок развертывания с помощью журналов аудита см. в статье [Устранение неполадок развертывания группы ресурсов в Azure](resource-manager-troubleshoot-deployments-portal.md).

Сведения из журналов аудита можно получить с помощью портала Azure, Azure PowerShell, интерфейса командной строки Azure, API REST Insights или с помощью [библиотеки .NET для Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## Просмотр журналов аудита с помощью портала

1. Чтобы просмотреть журналы аудита на портале, выберите **Обзор** и **Журналы аудита**.

    ![выбор журналов аудита](./media/resource-group-audit/select-audit-logs.png)

2. В колонке **Журналы аудита** вы увидите сводку последних операций для всех групп ресурсов в вашей подписке. Она включает графическое представление времени и состояния операций, а также список операций.

    ![отображение действий](./media/resource-group-audit/audit-summary.png)

3. Для поиска определенного типа действий можно отфильтровать операции, отображаемые в колонке журналов аудита. Выберите **Фильтр** в верхней части колонки.

    ![фильтрация журналов](./media/resource-group-audit/filter-logs.png)

4. В колонке **Фильтр** можно выбрать множество различных условий для ограничения количества отображаемых операций. Например, можно просмотреть все действия, выполненные конкретным пользователем на прошлой неделе.

    ![установка параметров фильтра](./media/resource-group-audit/set-filter.png)

После обновления окна просмотра отчетов вы увидите только те операции, которые удовлетворяют указанному условию. Эти параметры сохраняются до следующего просмотра журналов аудита, поэтому можно расширить эти критерии для увеличения количества отображаемых операций.

Также можно выполнить автоматическую фильтрацию для конкретного ресурса, выбрав журналы аудита из колонки этого ресурса. На портале выберите ресурс для аудита и щелкните **Журналы аудита**.

![аудит ресурсов](./media/resource-group-audit/audit-by-resource.png)

Обратите внимание, что журнал аудита для выбранного ресурса автоматически фильтруется за прошедшую неделю.

![фильтрация по ресурсам](./media/resource-group-audit/filtered-by-resource.png)

## Просмотр журналов аудита с помощью PowerShell

1. Чтобы получить записи журнала, выполните команду **Get-AzureRmLog**. Укажите дополнительные параметры, чтобы отфильтровать список записей. Если не указать время начала и окончания, возвращаются записи за последний час. Например, для получения операций для группы ресурсов за последний час выполните следующую команду:

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup

    В следующем примере показано, как использовать журнал аудита для анализа действий, выполненных в течение указанного времени. Даты начала и окончания указывайте в формате даты.

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

    Диапазон дат, например последние 14 дней, также можно указать с помощью функций даты.

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)

2. В зависимости от указанного времени начала приведенные выше команды могут вернуть длинный список операций для группы ресурсов. Чтобы найти в результатах нужную информацию, отфильтруйте их, используя условия поиска. Например, чтобы проанализировать обстоятельства остановки веб-приложения, выполните приведенную ниже команду. Вы увидите, что веб-приложение было остановлено пользователем someone@contoso.com.

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
        
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

        PS C:\> Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com

## Просмотр журналов аудита с помощью интерфейса командной строки Azure

1. Чтобы получить записи журнала, выполните команду **azure group log show**.

        azure group log show ExampleGroup

2. Вы можете отфильтровать результаты с помощью служебной программы JSON, например [jq](http://stedolan.github.io/jq/download/). В следующем примере показано, как найти операции, связанные с обновлением файла веб-конфигурации.

        azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

3. Вы можете найти действия для конкретного пользователя.

        azure group log show ExampleGroup --json | jq ".[] | select(.caller=="someone@contoso.com")"

## Просмотр журналов аудита с помощью API REST

Операции REST для работы с журналом аудита включены в интерфейс [REST API Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Получение событий журнала аудита описано в статье [Перечисление событий управления в подписке](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Дальнейшие действия

- Чтобы получить лучшее представление о действиях в вашей подписке, можно использовать журналы аудита Azure совместно с Power BI. Дополнительные сведения см. в записи в блоге [Просмотр и анализ журналов аудита Azure с помощью Power BI и др.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
- Дополнительные сведения о настройке политик безопасности см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).
- Сведения о командах для устранения неполадок развертывания см. в статье [Устранение неполадок развертывания группы ресурсов в Azure](resource-manager-troubleshoot-deployments-portal.md).
- Дополнительные сведения о предотвращении удаления ресурса для всех пользователей см. в статье [Блокировка ресурсов с Azure Resource Manager](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0323_2016-->