---
title: "Как просмотреть данные о совокупном состоянии работоспособности сущностей Azure Service Fabric | Документация Майкрософт"
description: "Описание того, как отправлять запросы, просматривать и оценивать сводные данные о работоспособности сущностей Azure Service Fabric при помощи запросов о работоспособности и общих запросов."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: fa34c52d-3a74-4b90-b045-ad67afa43fe5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2017
ms.author: oanapl
translationtype: Human Translation
ms.sourcegitcommit: 2573f300231ceb080343dad5c1c2aa65cc31c59e
ms.openlocfilehash: f62a7a8f594c67cac687159d72bfdb00a3355025
ms.lasthandoff: 01/13/2017


---
# <a name="view-service-fabric-health-reports"></a>Просмотр отчетов о работоспособности Service Fabric
В платформе Azure Service Fabric используется [модель работоспособности](service-fabric-health-introduction.md) , работа которой построена на сущностях работоспособности, на основе которых компоненты системы и устройства наблюдения создают отчеты по состояниям отслеживаемых локальных компонентов. [Хранилище данных о работоспособности](service-fabric-health-introduction.md#health-store) содержит все данные о работоспособности, с помощью которых можно определить состояние работоспособности сущностей.

По умолчанию кластер заполняется отчетами о работоспособности, отправляемыми компонентами системы. Подробнее об этом см. в статье [Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

В Service Fabric доступны несколько способов получения сводных данных о работоспособности сущностей:

* [обозреватель Service Fabric](service-fabric-visualizing-your-cluster.md) или другие средства визуализации;
* запросы о работоспособности (с помощью PowerShell, API или REST);
* общие запросы, возвращающие перечень сущностей, среди свойств которых есть работоспособность (с помощью PowerShell, API или REST).

Для демонстрации этих параметров мы будем использовать локальный кластер с пятью узлами. Рядом с приложением **fabric:/System** (присутствующим по умолчанию) расположены некоторые другие развернутые приложения. Одно из таких приложений — **fabric:/WordCount**. Это приложение содержит службу с отслеживанием состояния, настроенную на семь реплик. Так как есть только пять узлов, компоненты системы будут отображать предупреждения о том, что значение раздела ниже целевого.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Работоспособность в обозревателе Service Fabric
Обозреватель Service Fabric предоставляет визуальное представление кластера. На изображении ниже видно следующее:

* Приложение **fabric:/WordCount** выделено красным (ошибка), так как служба **MyWatchdog** вывела сообщение об ошибке при проверке свойства **Доступность**.
* Одна из его служб **fabric:/WordCount/WordCountService** выделена желтым (предупреждение). Так как служба настроена на семь реплик, их невозможно разместить полностью, потому что доступно лишь пять узлов. Раздел службы выделен желтым в соответствии с отчетом системы, хотя здесь это не показано. Если раздел выделен желтым цветом, таким же цветом будет выделена и служба.
* Кластер выделен красным, так как приложение выделено тем же цветом.

При оценке используются политики по умолчанию из манифеста кластера и манифеста приложения. Это строгие политики, при которых не допускаются сбои.

Так выглядит кластер в обозревателе Service Fabric:

![Вид кластера в обозревателе Service Fabric.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> Дополнительные сведения об [обозревателе Service Fabric](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Запросы о работоспособности
Service Fabric предоставляет запросы о работоспособности по каждому поддерживаемому [типу сущностей](service-fabric-health-introduction.md#health-entities-and-hierarchy). Их можно просмотреть с помощью API (методы находятся в **FabricClient.HealthManager**), командлетов PowerShell и REST. Эти запросы возвращают все сведения о работоспособности сущности: сводное состояние работоспособности, события работоспособности сущности, состояние работоспособности дочерних элементов (в соответствующих случаях) и оценка неработоспособности, если сущность неработоспособна.

> [!NOTE]
> Сущность работоспособности возвращается после ее полного заполнения в хранилище данных о работоспособности. Сущность должна быть активной (ее не следует удалять) и у нее должен быть системный отчет. У ее родительских сущностей в цепи иерархии также должны быть системные отчеты. Если любое из этих условий не выполнено, запросы о работоспособности будут возвращать исключение с причиной, по которой сущность нельзя возвратить.
>
>

Для запросов о работоспособности требуется передача идентификатора сущности, зависящего от ее типа. Запросы принимают необязательные параметры политики работоспособности. Если политики работоспособности не указаны, при оценке будут использоваться [политики работоспособности](service-fabric-health-introduction.md#health-policies) из манифеста кластера или приложения. Запросы также принимают фильтры, позволяя возвращать только частичные сведения о дочерних элементах или события, соответствующие условиям указанных фильтров.

> [!NOTE]
> На стороне сервера применяются выходные фильтры для сокращения размера ответных сообщений. Рекомендуем использовать выходные фильтры для ограничения объема возвращаемых данных, вместо того чтобы применять фильтры на стороне клиента.
>
>

Данные о работоспособности сущности содержат:

* Сводное состояние работоспособности сущности. Эти данные вычисляются в хранилище данных о работоспособности на основе отчетов о работоспособности сущности, состояния работоспособности дочерних элементов (в соответствующих случаях) и политик работоспособности. См. дополнительные сведения об [оценке работоспособности сущности](service-fabric-health-introduction.md#health-evaluation).  
* События работоспособности, связанные с сущностью.
* Коллекцию состояний работоспособности всех дочерних элементов сущностей, у которых есть такие элементы. В данных каждого состояния работоспособности содержится идентификатор сущности и сводное состояние работоспособности. Чтобы получить все сведения о работоспособности дочернего элемента, вызовите запрос о работоспособности для типа дочерней сущности и передайте идентификатор дочернего элемента.
* Оценки неработоспособности с данными об отчете, из-за которого активировано такое состояние сущности (если она неработоспособна).

## <a name="get-cluster-health"></a>Получение сведений о работоспособности кластера
Возвращает сведения о сущности кластера. В них содержатся данные о состоянии работоспособности приложений и узлов (дочерних элементов кластера). Входные данные:

* политика работоспособности кластера, используемая для оценки узлов и событий кластера (необязательный параметр);
* сопоставление политики работоспособности приложения с политиками работоспособности, которые используются для переопределения политик манифеста приложения (необязательный параметр);
* фильтры для событий, узлов и приложений, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события, узлы и приложения, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности кластера, создайте `FabricClient` и вызовите метод [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) в **HealthManager**.

Следующий вызов получает сведения о работоспособности кластера.

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

Следующий код получает сведения о работоспособности кластера, используя настраиваемую политику работоспособности кластера и фильтры для узлов и приложений. Он создает [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), где содержатся входные данные.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности кластера — [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

В этом примере кластер состоит из пяти узлов, а приложение системы и fabric:/WordCount настроены, как описано выше.

Следующий командлет возвращает сведения о работоспособности кластера, используя политики работоспособности по умолчанию. Сводное состояние работоспособности — "Предупреждение", так как приложение fabric:/WordCount находится в таком же состоянии. Обратите внимание, что в данных оценки неработоспособности содержатся подробные сведения об условиях, из-за которых активировано такое сводное состояние.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

Следующий командлет PowerShell возвращает сведения о работоспособности кластера, используя настраиваемую политику приложений. Он фильтрует результаты, позволяя возвращать только приложения и узлы с состоянием "Ошибка" или "Предупреждение". В результате ни один узел не будет возвращен, так как все узлы работоспособны. Только приложение fabric:/WordCount соответствует условиям фильтра приложений. Так как по условиям настраиваемой политики предупреждение для приложения fabric:/WordCount считается ошибкой, состояние приложения, как и кластера, оценивается как "Ошибка".

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности кластера с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707669.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707696.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="get-node-health"></a>Получение сведений о работоспособности узла
Возвращает сведения о работоспособности сущности узла и содержит события работоспособности, связанные с узлом. Входные данные:

* имя узла, определяющее узел (обязательный параметр);
* параметры политики работоспособности кластера, используемые для оценки работоспособности (необязательные параметры);
* фильтры для событий, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности узла через API, создайте `FabricClient` и вызовите метод [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) в HealthManager.

Следующий код получает сведения о работоспособности узла по указанному имени.

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

Следующий код получает сведения о работоспособности узла по указанному имени и передает фильтр событий и настраиваемую политику через [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx).

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности узла — [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .
Следующий командлет возвращает сведения о работоспособности узла, используя политики работоспособности по умолчанию:

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Следующий командлет возвращает сведения о работоспособности всех узлов в кластере:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности узла с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707650.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707665.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="get-application-health"></a>Получение сведений о работоспособности приложения
Возвращает сведения о работоспособности сущности приложения. В них содержатся данные о состоянии работоспособности развернутого приложения и дочерних элементов службы. Входные данные:

* имя приложения (URI), определяющее приложение (обязательный параметр);
* политика работоспособности приложения, используемая для переопределения политик манифеста приложения (необязательный параметр);
* фильтры для событий, служб и развернутых приложений, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события, службы и развернутые приложения, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности приложения, создайте `FabricClient` и вызовите метод [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) в HealthManager.

Следующий код получает сведения о работоспособности приложения по указанному имени (URI).

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

Следующий код получает сведения о работоспособности приложения по заданному имени (URI) с указанием фильтров и настраиваемых политик через [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности приложения — [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

Следующий командлет получает сведения о работоспособности приложения **fabric:/WordCount** .

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Следующий командлет PowerShell передает настраиваемые политики. Кроме того, он выполняет фильтрацию дочерних элементов и событий.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности приложения с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707681.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707643.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="get-service-health"></a>Получение сведений о работоспособности службы
Возвращает сведения о работоспособности сущности службы. В них содержатся данные о состоянии работоспособности разделов. Входные данные:

* имя службы (URI), определяющее службу (обязательный параметр);
* политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);
* фильтры событий и разделов, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события и разделы, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности службы через API, создайте `FabricClient` и вызовите метод [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) в HealthManager.

В следующем примере возвращаются сведения о работоспособности службы с указанным именем (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

Следующий код получает сведения о работоспособности службы по указанному имени, то есть универсальному коду ресурса (URI) службы, с указанием фильтров и настраиваемой политики через [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx).

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности службы — [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

Следующий командлет возвращает сведения о работоспособности службы, используя политику работоспособности по умолчанию:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности службы с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707609.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707646.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="get-partition-health"></a>Получение сведений о работоспособности раздела
Возвращает сведения о работоспособности сущности раздела. В них содержатся данные о состоянии работоспособности реплик. Входные данные:

* идентификатор раздела (GUID), определяющий раздел (обязательный параметр);
* политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);
* фильтры для событий и реплик, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события и реплики, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности раздела через API, создайте `FabricClient` и вызовите метод [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) в HealthManager. Чтобы задать необязательные параметры, создайте [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности раздела — [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

Следующий командлет предназначен для получения сведений о работоспособности всех разделов службы **fabric:/WordCount/WordCountService** .

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности раздела с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707683.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707680.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="get-replica-health"></a>Получение сведений о работоспособности реплики
Возвращает сведения о работоспособности реплики службы с отслеживанием состояния или экземпляра службы без отслеживания состояния. Входные данные:

* идентификатор раздела (GUID) и идентификатор реплики, определяющий реплику (обязательные параметры);
* параметры политики работоспособности приложения, используемые для переопределения политик манифеста приложения (необязательные параметры);
* фильтры для событий, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности реплики через API, создайте `FabricClient` и вызовите метод [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) в HealthManager. Чтобы указать дополнительные параметры, используйте [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности реплики — [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

Следующий командлет возвращает сведения о работоспособности первичной реплики для всех разделов службы:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности реплики с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707673.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707641.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="get-deployed-application-health"></a>Получение сведений о работоспособности развернутого приложения
Возвращает сведения о работоспособности приложения, развернутого на сущности узла. В них содержатся данные о состоянии работоспособности развернутых пакетов служб. Входные данные:

* имя приложения (URI) и имя узла (строка), определяющие развернутое приложение (обязательные параметры);
* политика работоспособности приложения, используемая для переопределения политик манифеста приложения (необязательный параметр);
* фильтры для событий и развернутых пакетов служб, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события и развернутые пакеты служб, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности приложения, развернутого на узле, через API, создайте `FabricClient` и вызовите метод [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) в HealthManager. Чтобы задать необязательные параметры, используйте [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности развернутых приложений — [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) . Чтобы узнать, где развернуто приложение, выполните командлет [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) и просмотрите дочерние элементы развернутого приложения.

Следующий командлет возвращает сведения о работоспособности приложения **fabric:/WordCount**, развернутого на узле **_Node_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности развернутого приложения с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707644.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707688.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="get-deployed-service-package-health"></a>Получение сведений о работоспособности развернутого пакета службы
Возвращает сведения о работоспособности сущности развернутого пакета службы. Входные данные:

* имя приложения (URI), имя узла (строка) и имя манифеста служб (строка), определяющие развернутый пакет службы (обязательные параметры);
* политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);
* фильтры для событий, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). При оценке сводного состояния работоспособности сущности используются все события, независимо от выбранного фильтра.

### <a name="api"></a>API
Чтобы получить сведения о работоспособности развернутого пакета службы через API, создайте `FabricClient` и вызовите метод [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) в HealthManager. Чтобы задать необязательные параметры, используйте [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности развернутого пакета службы — [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) . Чтобы узнать, где развернуто приложение, выполните командлет [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) и просмотрите развернутые приложения. Чтобы увидеть пакеты служб в приложении, просмотрите дочерние элементы развернутого пакета службы в выходных данных командлета [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) .

Следующий командлет возвращает сведения о работоспособности пакета службы **WordCountServicePkg** приложения **fabric:/WordCount**, развернутого на узле **_Node2_**. Эта сущность составляет отчеты **System.Hosting** при успешной активации пакета службы и точки входа, а также при успешной регистрации типа службы.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
Вы можете получить сведения о работоспособности развернутого пакета службы с помощью [запроса GET](https://msdn.microsoft.com/library/azure/dn707677.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/dn707689.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="health-chunk-queries"></a>Запросы фрагментов данных о работоспособности
Запросы фрагментов данных о работоспособности могут возвращать дочерние элементы многоуровневого кластера (рекурсивно) через входные фильтры. Они поддерживают расширенные фильтры, обеспечивающие большую гибкость при определении конкретных дочерних элементов, которые должны быть возвращены и обозначены уникальным идентификатором или другим идентификатором группы, а также при оценке работоспособности. По умолчанию дочерние элементы не обрабатываются, в отличие от команд работоспособности, которые обязательно включают дочерние элементы первого уровня.

[Запросы о работоспособности](service-fabric-view-entities-aggregated-health.md#health-queries) возвращают только дочерние элементы первого уровня указанной сущности, которые проходят через необходимые фильтры. Чтобы получить дочерние элементы дочерних элементов, необходимо вызвать дополнительные интерфейсы API работоспособности для каждой выбранной сущности. Аналогичным образом, чтобы получить сведения о работоспособности определенных сущностей, необходимо вызвать один интерфейс API работоспособности для каждой требуемой сущности. Усовершенствованные методы фильтрации запроса фрагментов данных позволяют указывать несколько элементов в одном запросе, сводя к минимуму размеры сообщений и их количество.

Преимущество запроса фрагмента заключается в том, что пользователи могут получить сведения о работоспособности для нескольких сущностей кластеров (потенциально для всех сущностей кластеров, начиная с обязательной корневой) в одном вызове. Например, можно составить сложный запрос на сведения о работоспособности.

* Возвращать только приложения с ошибками и включать для таких приложений все службы с состоянием "Предупреждение"|"Ошибка". Для возвращенных служб включать все разделы.
* Возвращать только сведения о состоянии 4 приложений, имена которых указаны.
* Возвращать только сведения о состоянии приложений требуемого типа.
* Возвращать все развернутые сущности на узле. Возвращает все приложения, все развернутые приложения на указанном узле и все развернутые пакеты служб на этом узле.
* Возвращать все реплики с ошибками. Возвращает все приложения, службы, разделы и только реплики с ошибками.
* Возвращать все приложения. Включить все разделы для указанной службы.

В настоящее время возможность запроса фрагмента данных о работоспособности предоставляется только для сущности кластера. Этот запрос возвращает фрагмент данных о работоспособности кластера, который содержит:

* объединенные сведения о работоспособности кластера;
* список узлов с учетом входных фильтров;
* список приложений с учетом входных фильтров; Каждый фрагмент данных о работоспособности приложения содержит список всех служб, для которых используются входные фильтры, и список всех развернутых приложений, которые поддерживают такие фильтры. То же касается дочерних элементов служб и развернутых приложений. Таким образом, все сущности в кластере при запросе могут быть возвращены в иерархическом виде.

### <a name="cluster-health-chunk-query"></a>Запрос фрагмента данных о работоспособности кластера
Возвращает сведения о работоспособности сущности кластера и содержит иерархические фрагменты данных о работоспособности для требуемых дочерних элементов. Входные данные:

* политика работоспособности кластера, используемая для оценки узлов и событий кластера (необязательный параметр);
* сопоставление политики работоспособности приложения с политиками работоспособности, которые используются для переопределения политик манифеста приложения (необязательный параметр);
* фильтры для узлов и приложений, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате (необязательный параметр). Фильтры соответствуют конкретной сущности или группам сущностей либо же применимы ко всем сущностям на этом уровне. Список фильтров может содержать один общий фильтр или фильтры для определенных идентификаторов сущностей с тонкой настройкой, которые возвращены запросом. Если настройки не установлены, по умолчанию дочерние элементы не возвращаются.
  См. дополнительные сведения о фильтрах в [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) и [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). Фильтры приложений могут рекурсивно указывать расширенные фильтры для дочерних элементов.

Результаты для фрагмента содержат дочерние элементы, которые поддерживают фильтры.

В настоящее время запрос фрагмента не возвращает оценки неработоспособности или события сущностей. Эти дополнительные сведения можно получить, используя имеющийся запрос на сведения о работоспособности кластера.

### <a name="api"></a>API
Чтобы получить фрагмент данных о работоспособности кластера, создайте `FabricClient` и вызовите метод [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) в **HealthManager**. Можно передать данные в [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) , чтобы описать политики работоспособности и расширенные фильтры.

Следующий код получает фрагмент данных о работоспособности кластера с расширенными фильтрами.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Командлет для получения сведений о работоспособности кластера — [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). Сначала подключитесь к кластеру, используя командлет [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) .

Следующий код получает данные узлов только в том случае, если они находятся в состоянии ошибки, за исключением определенного узла, который будет возвращаться при любых обстоятельствах.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

Следующий командлет возвращает фрагмент данных кластера с фильтрами приложения.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

Следующий командлет возвращает все сущности, развернутые на узле.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>REST
Вы можете получить фрагмент данных о работоспособности кластера с помощью [запроса GET](https://msdn.microsoft.com/library/azure/mt656722.aspx) или [запроса POST](https://msdn.microsoft.com/library/azure/mt656721.aspx), в который входят политики работоспособности, описанные в основном тексте.

## <a name="general-queries"></a>Общие запросы
Общие запросы возвращают перечень сущностей Service Fabric указанного типа. Их можно просмотреть с помощью API (используя методы **FabricClient.QueryManager**), а также командлетов PowerShell и REST. Эти запросы состоят из вложенных запросов от нескольких компонентов. Один из них — [хранилище данных о работоспособности](service-fabric-health-introduction.md#health-store), заполняющее сводные данные о состоянии работоспособности в каждом результате запроса.  

> [!NOTE]
> Общие запросы возвращают сводные данные о состоянии работоспособности сущности и не содержат подробных данных о работоспособности. Если сущность неработоспособна, вы можете отправить запросы о работоспособности, чтобы получить всю информацию о работоспособности, в том числе события, состояние работоспособности дочерних элементов и оценку неработоспособности.
>
>

Если после выполнения общего запроса возвращается неизвестное состояние, возможно, в хранилище данных о работоспособности нет всех данных о сущности. Но также возможно, что вложенный запрос к хранилищу завершился с ошибкой (например, в результате ошибки связи или регулирования хранилища данных о работоспособности). Отправьте дополнительный запрос о работоспособности сущности. При возникновении временных ошибок вложенных запросов, таких как проблемы с сетью, выполнение следующего запроса может завершиться успешно. В нем также могут содержаться дополнительные сведения из хранилища данных о работоспособности о том, почему не предоставлена сущность.

Ниже приведены запросы, возвращающие сведения о состоянии работоспособности ( **HealthState** ) для сущностей:

* Список узлов — возвращает список узлов в кластере (с разбивкой на страницы).
  * API — [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  * PowerShell: Get-ServiceFabricNode.
* Список приложений — возвращает список приложений в кластере (с разбивкой на страницы).
  * API — [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  * PowerShell: Get-ServiceFabricApplication.
* Список служб — возвращает список служб в приложении (с разбивкой на страницы).
  * API — [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  * PowerShell: Get-ServiceFabricService.
* Список разделов — возвращает список разделов в службе (с разбивкой на страницы).
  * API — [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  * PowerShell: Get-ServiceFabricPartition.
* Список реплик — возвращает список реплик в разделе (с разбивкой на страницы).
  * API — [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  * PowerShell: Get-ServiceFabricReplica.
* Список развернутых приложений — возвращает список развернутых приложений на узле.
  * API — [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  * PowerShell: Get-ServiceFabricDeployedApplication.
* Список развернутых пакетов служб — возвращает список пакетов служб в развернутом приложении.
  * API — [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  * PowerShell: Get-ServiceFabricDeployedApplication.

> [!NOTE]
> Некоторые запросы возвращают результаты с разбивкой на страницы. В результатах этих запросов возвращается список, производный от [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Если результаты не соответствуют сообщению, возвращается только страница и устанавливается маркер ContinuationToken, который отслеживает остановку перечисления. Чтобы получить следующие результаты, необходимо продолжать вызывать тот же запрос, передавая маркер продолжения из предыдущего запроса.
>
>

### <a name="examples"></a>Примеры
Следующий код возвращает неработоспособные приложения в кластере.

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Следующий командлет возвращает сведения о приложении fabric:/WordCount. Обратите внимание, что состояние работоспособности — "Предупреждение".

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

Следующий командлет возвращает службы с состоянием работоспособности "Предупреждение":

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>Обновление кластера и приложений
Во время отслеживаемого обновления кластера и приложений Service Fabric проверяет все компоненты, чтобы обеспечить их непрерывную работоспособность. Если в результате оценки с использованием настроенных политик работоспособности окажется, что у сущности неработоспособное состояние, при обновлении будут применены соответствующие ему политики, чтобы определить, какое действие должно быть следующим. Обновление можно приостановить, чтобы пользователь мог внести изменения (например, исправить ошибки или изменить политики). Кроме того, можно выполнить автоматический откат до предыдущей рабочей версии.

Во время обновления *кластера* можно узнать состояние этого процесса. Эти сведения включают в себя данные об оценках неработоспособности, которые указывают, что не работает в кластере. Если выполняется откат обновления из-за проблем работоспособности, состояние обновления будет содержать последние причины неработоспособности. Используя эти данные, администраторы могут определить, что пошло не так после отката или остановки обновления.

Кроме того, во время обновления *приложения* можно просмотреть результаты оценки неработоспособности (содержатся в состоянии обновления).

Далее показано состояние обновления для измененного приложения fabric:/WordCount. Устройство наблюдения сообщило об ошибке на одной из реплик. Выполняется откат обновления из-за несоответствия условиям проверок работоспособности.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Дополнительные сведения см. в статье [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Использование оценки работоспособности для устранения неполадок
При возникновении проблемы с кластером или приложением просмотрите сведения о работоспособности кластера или приложения, чтобы выявить неполадку. Данные оценки неработоспособности содержат подробные сведения о причине активации текущего состояния неработоспособности. При необходимости можно детализировать неработоспособные дочерние сущности, чтобы определить основную причину.

> [!NOTE]
> Оценки неработоспособности указывают на первую причину, по которой была получена текущая оценка работоспособности сущности. Возможно, есть другие события, которые вызывают это состояние, но они не отражаются в оценках. Чтобы получить дополнительную информацию, определите все отчеты о неработоспособности в кластере, просмотрев подробные сведения о сущностях работоспособности.
>
>

## <a name="next-steps"></a>Дальнейшие действия
[Устранение неполадок с помощью отчетов о работоспособности системы](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Добавление настраиваемых отчетов о работоспособности Service Fabric](service-fabric-report-health.md)

[Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

