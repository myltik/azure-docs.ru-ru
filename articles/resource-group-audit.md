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
	ms.date="09/10/2015" 
	ms.author="tomfitz"/>

# Операции аудита с помощью диспетчера ресурсов

Когда во время развертывания или жизненного цикла решения возникают проблемы, нужно понять, что пошло не так. Диспетчер ресурсов предоставляет два способа, которые позволяют выяснить, что произошло и почему это произошло. С помощью команд развертывания можно получить сведения о конкретном развертывании и операциях. Получить сведения о развертывании и других действиях, выполняемых в течение жизненного цикла решения, также можно с помощью журналов аудита. Этот раздел посвящен журналам аудита.

Журнал аудита содержит все действия, выполненные с ресурсами. Следовательно, если пользователь в организации изменяет ресурс, вы можете определить действие, время и пользователя.

Сведения из журналов аудита можно получить с помощью Azure PowerShell, Azure CLI, REST API или на портале предварительной версии Azure.

## PowerShell

Чтобы получить записи журнала, выполните команду **Get-AzureResourceGroupLog**. Предоставьте дополнительные параметры, чтобы отфильтровать список записей.

В следующем примере показано, как использовать журнал аудита для анализа действий, выполненных во время жизненного цикла решения. Можно просмотреть дату и время выполнения действия, а также пользователя, который его вызвал.

    PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00

В зависимости от указанного времени начала предыдущая команда может вернуть длинный список действий для данной группы ресурсов. Чтобы найти в результатах нужную информацию, отфильтруйте их, используя условия поиска. Например, если нужно проанализировать, как было остановлено веб-приложение, выполните приведенную ниже команду. Вы увидите, что действие остановки было выполнено пользователем someone@example.com.

    PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

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

В следующем примере мы просто будем искать действия, завершившиеся сбоем после указанного времени запуска. Мы также включим параметр **DetailedOutput**, чтобы просмотреть сообщения об ошибках.

    PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroup -StartTime 2015-08-27T12:00 -Status Failed –DetailedOutput
    
Если эта команда возвращает слишком много записей и свойств, можно ограничиться данными аудита, получив свойство **properties**.

    PS C:\> (Get-AzureResourceGroupLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

И наоборот, можно уточнить результаты, просмотрев сообщение о состоянии.

    PS C:\> (Get-AzureResourceGroupLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Инфраструктура CLI Azure

Чтобы получить записи журнала, выполните команду **azure group log show**.

    azure group log show ExampleGroup

Вы можете отфильтровать результаты с помощью такой служебной программы JSON, как [jq](http://stedolan.github.io/jq/download/). В следующем примере показано, как искать операции, связанные с обновлением файла веб-конфигурации.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

Можно добавить параметр **--last-deployment**, чтобы ограничить возвращаемые элементы только операциями, выполненными в ходе последнего развертывания.

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

Операции REST для работы с журналом аудита являются частью [REST API Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Сведения о событиях журнала аудита можно получить с помощью [перечисления событий управления в подписке](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Портал предварительной версии

Зарегистрированные в журнале операции можно также просмотреть на портале предварительной версии. Просто откройте колонку журналов аудита.

![выбор журналов аудита](./media/resource-group-audit/select-audit.png)

И просмотрите список последних операций.

![отображение действий](./media/resource-group-audit/show-actions.png)

Вы можете выбрать любую операцию, чтобы просмотреть дополнительные сведения о ней.

## Дальнейшие действия

- Дополнительные сведения о настройке политик безопасности см. в статье [Управление доступом к ресурсам](./azure-portal/resource-group-rbac.md).
- Инструкции по предоставлению доступа к ресурсам субъекта-службы см. в статье [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](resource-group-authenticate-service-principal.md).
- Дополнительные сведения о действиях ресурса для всех пользователей см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

<!---HONumber=Sept15_HO3-->