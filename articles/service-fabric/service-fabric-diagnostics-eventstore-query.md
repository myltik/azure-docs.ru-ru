---
title: Запрос событий кластера с помощью интерфейсов API EventStore в кластерах Azure Service Fabric | Документация Майкрософт
description: Узнайте, как использовать интерфейсы API EventStore в Azure Service Fabric для запроса событий платформы.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 5c184841602f269555ce2196ef660faba14dbf8a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204616"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Выполнение запросов к интерфейсам API EventStore для получения событий кластера

В этой статье описывается, как выполнить запрос к интерфейсам API EventStore, которые доступны в Service Fabric версии 6.2 и более поздней версии. Если вы хотите узнать больше о службе EventStore, прочитайте [обзор службы EventStore](service-fabric-diagnostics-eventstore.md). В настоящее время службе EventStore доступны только данные за последние 7 дней (это обусловлено диагностической политикой хранения данных кластера).

>[!NOTE]
>В версии Service Fabric 6.2 интерфейсы API EventStore находятся на этапе предварительной версии для кластеров Windows, работающих только в Azure. Мы работаем над переносом этих функциональных возможностей в Linux, а также в изолированные кластеры.

К интерфейсам API EventStore можно обращаться напрямую через конечную точку REST или программно. В зависимости от запроса требуется указать несколько параметров, которые необходимы для сбора соответствующих данных. Как правило, это:
* `api-version`: версия интерфейсов API EventStore, которую вы используете.
* `StartTimeUtc`: определяет начало запрашиваемого периода.
* `EndTimeUtc`: конец периода времени.

Помимо этого доступны дополнительные параметры, в том числе:
* `timeout`: позволяет переопределить времени ожидания по умолчанию (60 с) для выполнения операции запроса.
* `eventstypesfilter`: позволяет фильтровать события по типу.
* `ExcludeAnalysisEvents`: исключает из результатов события анализа. По умолчанию запросы EventStore будут возвращать события анализа, если это возможно. События анализа — это более подробные события операционного канала, которые содержат дополнительный контекст и сведения по сравнению с обычными событиями Service Fabric и обеспечивают более подробную информацию.
* `SkipCorrelationLookup`: позволяет не выполнять поиск потенциальных связанных событий в кластере. По умолчанию EventStore попытается сопоставить события в кластере и связать события между собой, когда это возможно. 

У каждой сущности в кластере можно запросить события. Можно также запрашивать события для всех сущностей определенного типа. Например, можно запросить события для конкретного узла или для всех узлов в кластере. Текущий набор сущностей, для которых вы можете запрашивать события (и структура запроса):
* кластер: `/EventsStore/Cluster/Events`;
* узлы: `/EventsStore/Nodes/Events`;
* узел: `/EventsStore/Nodes/<NodeName>/$/Events`;
* приложения: `/EventsStore/Applications/Events`;
* приложение: `/EventsStore/Applications/<AppName>/$/Events`;
* службы: `/EventsStore/Services/Events`;
* служба: `/EventsStore/Services/<ServiceName>/$/Events`;
* секции: `/EventsStore/Partitions/Events`;
* секция: `/EventsStore/Partitions/<PartitionID>/$/Events`;
* реплики: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`;
* реплика: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`.

>[!NOTE]
>При ссылке на имя приложения или службы запрос может не содержать префикс fabric:/. Кроме того, если имена приложений или служб содержат косую черту "/", замените ее знаком "~", чтобы сохранить работоспособность запроса. Например, если приложение отображается как fabric:/App1/FrontendApp, то в запросах к этому приложению оно имело бы следующий вид: `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Кроме того, сейчас отчеты о работоспособности служб отображаются под соответствующим приложением, поэтому следует запрашивать события `DeployedServiceHealthReportCreated` для соответствующей сущности приложения. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Отправка запроса к EventStore через конечные точки REST API

Вы можете отправить запрос к EventStore напрямую через конечную точку REST, выполнив запросы `GET` к `<your cluster address>/EventsStore/<entity>/Events/`.

Например, запрос всех событий кластера с `2018-04-03T18:00:00Z` до `2018-04-04T18:00:00Z` выглядит следующим образом.

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.2-preview&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Он может вернуть ошибку, если события отсутствуют, а при успешном выполнении запрос вернет события в формате JSON.

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

Как видите, после первого запуска с `2018-04-03T18:00:00Z` до `2018-04-04T18:00:00Z` этот кластер успешно завершил свое первое обновление с `"CurrentClusterVersion": "0.0.0.0:"` до `"TargetClusterVersion": "6.2:1.0"` за `"OverallUpgradeElapsedTimeInMs": "120196.5212"` мс.

## <a name="query-the-eventstore-programmatically"></a>Отправка запроса к EventStore программным способом

Можно также выполнить запрос к EventStore программно при помощи [ клиентской библиотеки Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Настроив клиент Service Fabric, вы сможете запрашивать события, обращаясь к EventStore следующим образом: ` sfhttpClient.EventStore.<request>`.

Ниже приведен пример запроса всех событий кластера с `2018-04-03T18:00:00Z` до `2018-04-04T18:00:00Z` с помощью функции `GetClusterEventListAsync`.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

## <a name="sample-scenarios-and-queries"></a>Примеры сценариев и запросов

Вот несколько примеров того, как можно вызвать интерфейсы REST API хранилища событий для получения представления о состоянии кластера.

*Обновления кластера*

Чтобы узнать время последнего успешного обновления или попытки обновления кластера на прошлой неделе, можно отправить запрос к интерфейсам API, чтобы получить данные о недавно завершенных обновлениях кластера. Для этого нужно запросить события ClusterUpgradeComplete в EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeComplete`.

*Проблемы при обновлении кластера*

Аналогично, если при последнем обновлении кластера возникли проблемы, может запросить все события для сущности кластера. Вы увидите различные события, включая запуск обновлений и каждый домен обновления, для которого обновление прошло успешно. Также вы увидите события на момент, когда начался откат, и соответствующие события работоспособности. Запрос, который можно использовать для получения этих сведений: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Изменения состояния узлов*

Чтобы просмотреть изменения состояния узлов за последние несколько дней (запуск и остановка узлов, а также их активация или деактивация (платформой, службой тестирования в условиях хаоса или пользователем)), используйте следующий запрос: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`.

*События приложений*

Вы также можете отслеживать последние развертывания и обновления приложений. Используйте следующий запрос, чтобы увидеть все события, связанные с приложениями, в кластере: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`.

*Хронологические данные о работоспособности приложения*

Помимо событий жизненного цикла приложений может понадобиться просмотреть хронологические данные о работоспособности определенного приложения. Для этого можно указать имя приложения, для которого нужно собрать данные. Используйте данный запрос, чтобы получить все события работоспособности приложения: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.2-preview&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ProcessApplicationReport`. Если вы хотите включить в результаты события работоспособности с истекшим сроком действия (то есть с истекшим сроком жизни), добавьте `,ExpiredDeployedApplicationEvent` в конец запроса, чтобы использовать фильтр по двум типам событий.

*Хронологические данные о работоспособности для всех служб в myApp*

В настоящее время события отчета о работоспособности для служб отображаются как события `DeployedServiceHealthReportCreated` для соответствующей сущности приложения. Чтобы увидеть, как ваши службы функционируют для App1, используйте следующий запрос: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServiceHealthReportCreated`.

*Перенастройка секций*

Чтобы просмотреть все перемещения секций, которые произошли в кластере, запросите событие `ReconfigurationCompleted`. Это поможет выяснить, когда и какие рабочие нагрузки выполнялись на том или ином узле, при диагностике проблем в кластере. Вот пример запроса для этого: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.2-preview&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigurationCompleted`.

*Служба тестирования в условиях хаоса*

Это событие запуска или остановки службы тестирования в условиях хаоса, представленной на уровне кластера. Для просмотра последних данных об использовании службы тестирования в условиях хаоса выполните следующий запрос: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`.

