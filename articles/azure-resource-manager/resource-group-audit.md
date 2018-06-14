---
title: Просмотр журналов действий Azure для наблюдения за ресурсами | Документация Майкрософт
description: Просмотр действий пользователя и ошибок с помощью журнала действий. Отображаются портал Azure, PowerShell, интерфейс командной строки Azure и REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: 2dcf93a635a8eb0a01ec266d2478b6e5a336ec00
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358699"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Просмотр журналов действий для аудита действий с ресурсами

С помощью журналов действий можно определить:

* какие операции выполнялись с ресурсами в вашей подписке;
* кто инициировал операцию (при этом для операций, инициированных серверной службой, имя вызывающего пользователя не возвращается); 
* когда была выполнена операция;
* состояние операции;
* значения других свойств, которые могут помочь в изучении операции.

Журнал изменений содержит все операции записи (PUT, POST, DELETE), выполненные с ресурсами. Он не содержит операции чтения (GET). Список действий ресурсов см. в статье [Операции поставщиков ресурсов Azure Resource Manager](../role-based-access-control/resource-provider-operations.md). Журналы аудита можно использовать для поиска ошибки при устранении неполадок, а также для наблюдения за тем, как пользователь в вашей организации изменяет ресурс.

Журналы изменений сохраняются в течение 90 дней. Вы можете запросить любой диапазон дат, при условии, что дата начала не старше 90 дней в прошлом.



Сведения из журналов действий можно получить с помощью портала, PowerShell, интерфейса командной строки Azure, API REST Insights или с помощью [библиотеки .NET для Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Microsoft Azure

1. Чтобы просмотреть журналы действий на портале, выберите **Монитор**.
   
    ![просмотр журналов действий](./media/resource-group-audit/select-monitor.png)

   Или, чтобы автоматически отфильтровать журнал действий по определенному ресурсу или группе ресурсов, выберите **Журнал действий**. Обратите внимание, что журнал действий автоматически фильтруется по выбранному ресурсу.
   
    ![фильтрация по ресурсам](./media/resource-group-audit/filtered-by-resource.png)
2. В колонке **Журнал действий** можно просмотреть сводку последних операций.
   
    ![отображение действий](./media/resource-group-audit/audit-summary.png)
3. Чтобы ограничить количество отображаемых операций, выберите различные условия. Например, на следующем рисунке показано, как изменение полей **Временной диапазон** и **Кем инициировано событие** позволяет просмотреть действия конкретного пользователя или приложения за прошлый месяц. Выберите **Применить** для просмотра результатов запроса.
   
    ![установка параметров фильтра](./media/resource-group-audit/set-filter.png)

4. Если вы хотите повторить тот же запрос позже, выберите **Сохранить** и укажите имя запроса.
   
    ![сохранение запроса](./media/resource-group-audit/save-query.png)
5. Чтобы быстро выполнить запрос, можно выбрать один из встроенных запросов, например запрос невыполненных развертываний.

    ![Выбор запроса](./media/resource-group-audit/select-quick-query.png)

   Выбранный запрос автоматически задает необходимые значения фильтра.

    ![Просмотр ошибок развертывания](./media/resource-group-audit/view-failed-deployment.png)   

6. Выберите одну из операций, чтобы просмотреть сводку по событию.

    ![Просмотр операции](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell

1. Чтобы получить записи журнала, выполните команду **Get-AzureRmLog** . Укажите дополнительные параметры, чтобы отфильтровать список записей. Если не указать время начала и окончания, возвращаются записи за последний час. Например, для получения операций для группы ресурсов за последний час выполните следующую команду:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    В следующем примере показано, как использовать журнал действий для анализа действий, выполненных в течение указанного времени. Даты начала и окончания указывайте в формате даты.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Диапазон дат, например последние 14 дней, также можно указать с помощью функций даты.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. В зависимости от указанного времени начала приведенные выше команды могут вернуть длинный список операций для группы ресурсов. Чтобы найти в результатах нужную информацию, отфильтруйте их, используя условия поиска. Например, чтобы проанализировать обстоятельства остановки веб-приложения, выполните приведенную ниже команду.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    В нашем примере вы видите, что веб-приложение было остановлено пользователем someone@contoso.com. 

  ```powershell 
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
  ```

3. Можно найти действия, выполненные конкретным пользователем, даже для группы ресурсов, которой больше не существует.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Можно применить фильтр для невыполненных операций.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Можно получить сведения об одной ошибке, просмотрев сообщение о состоянии для ее записи.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Возвращаемые данные:
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы получить записи журнала, выполните команду [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

  ```azurecli
  az monitor activity-log list --resource-group <group name>
  ```


## <a name="rest-api"></a>ИНТЕРФЕЙС REST API

Операции REST для работы с журналом действий включены в интерфейс [REST API Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Получение событий журнала действий описано в статье [Список событий управления в подписке](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Дополнительная информация

* Чтобы получить больше информации о действиях в вашей подписке, можно использовать журналы аудита Azure совместно с Power BI. Дополнительные сведения см. в записи блога [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Журналы аудита Azure в Power BI: просмотр, анализ и другие возможности).
* Дополнительные сведения о настройке политик безопасности см. в статье о [контроле доступа на основе ролей Azure](../role-based-access-control/role-assignments-portal.md).
* Чтобы узнать о командах для просмотра операций развертывания, ознакомьтесь с разделом [View deployment operations with Azure Resource Manager](resource-manager-deployment-operations.md) (Просмотр операций развертывания с помощью Azure Resource Manager).
* Вы можете запретить всем пользователям операции удаления для определенного ресурса, как описано в статье [Блокировка ресурсов с помощью Azure Resource Manager](resource-group-lock-resources.md).
* Список операций, доступных для каждого поставщика Microsoft Azure Resource Manager, см. в статье [Операции поставщиков ресурсов Azure Resource Manager](../role-based-access-control/resource-provider-operations.md).

