<properties 
	pageTitle="Операции аудита с помощью диспетчера ресурсов | Microsoft Azure" 
	description="Просмотр действий пользователя и ошибок с помощью журнала аудита в диспетчере ресурсов. Отображение PowerShell, Azure CLI и REST." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="tomfitz"/>

# Операции аудита с помощью диспетчера ресурсов

Когда во время развертывания или жизненного цикла решения возникают проблемы, нужно понять, что пошло не так. Диспетчер ресурсов предоставляет два способа, которые позволяют выяснить, что произошло и почему это произошло. С помощью команд развертывания можно получить сведения о конкретном развертывании и операциях. Получить сведения о развертывании и других действиях, выполняемых в течение жизненного цикла решения, также можно с помощью журналов аудита. Этот раздел посвящен журналам аудита.

Журнал аудита содержит все действия, выполненные с ресурсами. Следовательно, если пользователь в организации изменяет ресурс, вы можете определить действие, время и пользователя.

При работе с журналами аудита следует помнить о двух важных ограничениях.

1. Журналы аудита сохраняются только в течение 90 дней.
2. Вы можете отправить запрос на получение данных за период не более 15 дней.

Сведения из журналов аудита можно получить с помощью Azure PowerShell, Azure CLI, REST API или на портале Azure.

## PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Для получения записей журнала выполните команду **Get-AzureRmLog** (или **Get-AzureResourceGroupLog** для версий PowerShell, выпущенных до версии 1.0). Предоставьте дополнительные параметры, чтобы отфильтровать список записей.

В следующем примере показано, как использовать журнал аудита для анализа действий, выполненных во время жизненного цикла решения. Можно просмотреть дату и время выполнения действия, а также пользователя, который его вызвал. Даты начала и окончания указывайте в формате даты.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

Также вы можете использовать функции даты, чтобы указать диапазон дат, например последние 15 дней.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15)

В зависимости от указанного времени начала приведенные выше команды могут вернуть длинный список действий для группы ресурсов. Чтобы найти в результатах нужную информацию, отфильтруйте их, используя условия поиска. Например, чтобы проанализировать обстоятельства остановки веб-приложения, выполните приведенную ниже команду. Вы увидите, что действие остановки было выполнено пользователем someone@example.com.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

В следующем примере мы просто будем искать действия, завершившиеся сбоем после указанного времени запуска. Также добавим параметр **DetailedOutput**, чтобы просмотреть сообщения об ошибках.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) -Status Failed –DetailedOutput
    
Если эта команда возвращает слишком много записей и свойств, вы можете ограничить диапазон просмотра свойством **properties**.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

И наоборот, можно уточнить результаты, просмотрев сообщение о состоянии.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Инфраструктура CLI Azure

Чтобы получить записи журнала, выполните команду **azure group log show**.

    azure group log show ExampleGroup

Вы можете отфильтровать результаты с помощью служебной программы JSON, например [jq](http://stedolan.github.io/jq/download/). В следующем примере показано, как искать операции, связанные с обновлением файла веб-конфигурации.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

Можно добавить параметр **--last-deployment**, чтобы возвращаемые элементы содержали только операции последнего развертывания.

    azure group log show ExampleGroup --last-deployment

Если список операций в рамках последнего развертывания слишком длинный, можно отфильтровать результаты, ограничившись операциями, завершившимися сбоем.

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## Интерфейс REST API

Операции REST для работы с журналом аудита включены в интерфейс [REST API Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Получение событий журнала аудита описано в статье [Перечисление событий управления в подписке](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Портал

Зарегистрированные в журнале операции можно также просмотреть на портале. Просто откройте колонку журналов аудита.

![выбор журналов аудита](./media/resource-group-audit/select-audit.png)

И просмотрите список последних операций.

![отображение действий](./media/resource-group-audit/show-actions.png)

Вы можете выбрать любую операцию, чтобы просмотреть дополнительные сведения о ней.

## Дальнейшие действия

- Дополнительные сведения о настройке политик безопасности см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).
- Предоставление доступа к субъекту-службе описано в статье [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](resource-group-authenticate-service-principal.md).
- Дополнительные сведения о блокировке действий с ресурсом для всех пользователей см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_1223_2015-->