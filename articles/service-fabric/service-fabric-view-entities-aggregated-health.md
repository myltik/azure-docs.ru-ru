<properties
   pageTitle="Обзор сводного состояния работоспособности сущностей Azure Service Fabric | Microsoft Azure"
   description="Описание того, как отправлять запросы, просматривать и оценивать сводные данные о работоспособности сущностей Azure Service Fabric при помощи запросов о работоспособности и общих запросов."
   services="service-fabric"HealthManager
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# Просмотр отчетов о работоспособности Service Fabric
В платформе Azure Service Fabric используется [модель работоспособности](service-fabric-health-introduction.md), работа которой построена на сущностях работоспособности, на основе которых компоненты системы и устройства наблюдения создают отчеты по состояниям отслеживаемых локальных компонентов. [Хранилище данных о работоспособности](service-fabric-health-introduction.md#health-store) содержит все данные о работоспособности, с помощью которых можно определить состояние работоспособности сущностей.

По умолчанию кластер заполняется отчетами о работоспособности, отправляемыми компонентами системы. Подробнее об этом см. в статье [Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

В Service Fabric доступны несколько способов получения сводных данных о работоспособности сущностей:

- [обозреватель Service Fabric](service-fabric-visualizing-your-cluster.md) или другие средства визуализации;

- запросы о работоспособности (с помощью PowerShell, API или REST);

- общие запросы, возвращающие перечень сущностей, среди свойств которых есть работоспособность (с помощью PowerShell, API или REST).

Для демонстрации этих параметров мы будем использовать локальный кластер с пятью узлами. Рядом с приложением **fabric:/System** (присутствующим по умолчанию) расположены некоторые другие развернутые приложения. Одним из них является **fabric:/WordCount**. Это приложение содержит службу с отслеживанием состояния, настроенную на семь реплик. Так как есть только пять узлов, компоненты системы будут отображать предупреждения о том, что значение раздела ниже целевого.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Работоспособность в обозревателе Service Fabric
Обозреватель Service Fabric предоставляет визуальное представление кластера. На изображении ниже видно следующее:

- Приложение **fabric:/WordCount** выделено красным (ошибка), так как служба **MyWatchdog** вывела сообщение об ошибке при проверке свойства **Доступность**.

- Одна из его служб **fabric:/WordCount/WordCount.Service** выделена желтым (предупреждение). Как описано выше, служба настроена на семь реплик, которые невозможно разместить полностью (так как доступно лишь пять узлов). Раздел службы выделен желтым в соответствии с отчетом системы, хотя здесь это не показано. Если раздел выделен желтым цветом, таким же цветом будет выделена и служба.

- Кластер выделен красным, так как приложение выделено тем же цветом.

При оценке используются политики по умолчанию из манифеста кластера и манифеста приложения. Это строгие политики, при которых не допускаются сбои.

Так выглядит кластер в обозревателе Service Fabric:

![Вид кластера в обозревателе Service Fabric.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Дополнительные сведения об [обозревателе Service Fabric](service-fabric-visualizing-your-cluster.md).

## Запросы о работоспособности
Service Fabric предоставляет запросы о работоспособности по каждому поддерживаемому [типу сущностей](service-fabric-health-introduction.md#health-entities-and-hierarchy). Их можно просмотреть с помощью API (методы находятся в **FabricClient.HealthManager**), командлетов PowerShell и REST. Эти запросы возвращают все сведения о работоспособности сущности, в том числе сводное состояние работоспособности, события работоспособности, связанные с сущностью, состояние работоспособности дочерних элементов (в соответствующих случаях) и оценку неработоспособности, если сущность неработоспособна.

> [AZURE.NOTE] Сущность работоспособности возвращается пользователю после ее полного заполнения в хранилище данных о работоспособности. Сущность должна быть активной (ее не следует удалять) и у нее должен быть системный отчет. У ее родительских сущностей в цепи иерархии также должны быть системные отчеты. Если любое из этих условий не выполнено, запросы о работоспособности будут возвращать исключение с причиной, по которой сущность нельзя возвратить.

Для запросов о работоспособности требуется передача идентификатора сущности, зависящего от ее типа. Запросы принимают необязательные параметры политики работоспособности. Если не указать эти параметры, при оценке будут использоваться [политики работоспособности](service-fabric-health-introduction.md#health-policies) из манифеста кластера или приложения. Запросы также принимают фильтры, позволяя возвращать только частичные сведения о дочерних элементах или события, соответствующие условиям указанных фильтров.

> [AZURE.NOTE] На стороне сервера применяются выходные фильтры для сокращения размера ответных сообщений. Рекомендуем использовать выходные фильтры для ограничения объема возвращаемых данных, вместо того чтобы применять фильтры на стороне клиента.

Данные работоспособности сущности содержат следующие сведения:

- Сводное состояние работоспособности сущности. Эти данные вычисляются в хранилище данных о работоспособности на основе отчетов о работоспособности сущности, состояния работоспособности дочерних элементов (в соответствующих случаях) и политик работоспособности. См. дополнительные сведения об [оценке работоспособности сущности](service-fabric-health-introduction.md#entity-health-evaluation).  

- События работоспособности, связанные с сущностью.

- Коллекцию состояний работоспособности всех дочерних элементов сущностей, у которых есть такие элементы. В данных каждого состояния работоспособности содержится идентификатор сущности и сводное состояние работоспособности. Чтобы получить все сведения о работоспособности дочернего элемента, вызовите запрос о работоспособности для типа дочерней сущности и передайте идентификатор дочернего элемента.

- Оценки неработоспособности с данными об отчете, из-за которого активировано такое состояние сущности (если она неработоспособна).

## Получение сведений о работоспособности кластера
Возвращает сведения о сущности кластера. В них содержатся данные о состоянии работоспособности приложений и узлов (дочерних элементов кластера). Входные данные:

- сопоставление политики работоспособности приложения с политиками работоспособности, которые используются для переопределения политик манифеста приложения (необязательный параметр);

- фильтры для событий, узлов и приложений, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события, узлы и приложения, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности кластера, создайте **FabricClient** и вызовите метод [**GetClusterHealthAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) в **HealthManager**.

Следующий сценарий возвращает сведения о работоспособности кластера:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

Следующий сценарий возвращает сведения о работоспособности кластера, используя настраиваемую политику работоспособности кластера и фильтры для узлов и приложений. Обратите внимание, что он создает **System.Fabric.Description.[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)**, где содержатся все входные данные.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности кластера — **[Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**.

В этом примере кластер состоит из пяти узлов, а приложение системы и fabric:/WordCount настроены, как указано выше.

Следующий командлет возвращает сведения о работоспособности кластера, используя политики работоспособности по умолчанию. Сводное состояние работоспособности — "Предупреждение", так как приложение fabric:/WordCount находится в таком же состоянии. Обратите внимание, что в данных оценки неработоспособности содержатся подробные сведения об условиях, из-за которых активировано такое сводное состояние.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

Следующий командлет PowerShell возвращает сведения о работоспособности кластера, используя настраиваемую политику приложений. Он фильтрует результаты, позволяя возвращать только приложения и узлы с состоянием "Ошибка" или "Предупреждение". В результате ни один узел не будет возвращен, так как все узлы работоспособны. Только приложение fabric:/WordCount соответствует условиям фильтра приложений. Так как по условиям настраиваемой политики предупреждение для приложения fabric:/WordCount считается ошибкой, состояние приложения, как и кластера, оценивается как "Ошибка".

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Получение сведений о работоспособности узла
Возвращает сведения о работоспособности сущности узла, в которых также содержатся события работоспособности, связанные с узлом. Входные данные:

- имя узла, определяющее узел (обязательный параметр);

- параметры политики работоспособности кластера, используемые для оценки работоспособности (необязательные параметры);

- фильтры для событий, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности узла через API, создайте FabricClient и вызовите метод **GetNodeHealthAsync** в HealthManager.

Следующий сценарий возвращает сведения о работоспособности узла по указанному имени:

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

Следующий сценарий возвращает сведения о работоспособности узла по указанному имени и передает фильтр событий и настраиваемую политику через **System.Fabric.Description.[NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)**:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности узла — **Get-ServiceFabricNodeHealth**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**. Следующий командлет возвращает сведения о работоспособности узла, используя политики работоспособности по умолчанию:

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

Следующий командлет возвращает сведения о работоспособности всех узлов в кластере:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## Получение сведений о работоспособности приложения
Возвращает сведения о работоспособности сущности приложения. В них содержатся данные о состоянии работоспособности развернутого приложения и дочерних элементов службы. Входные данные:

- имя приложения (URI), определяющее приложение (обязательный параметр);

- политика работоспособности приложения, используемая для переопределения политик манифеста приложения (необязательный параметр);

- фильтры для событий, служб и развернутых приложений, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события, службы и развернутые приложения, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности приложения, создайте FabricClient и вызовите метод **GetApplicationHealthAsync** в HealthManager.

Следующий сценарий возвращает сведения о работоспособности приложения по указанному имени (URI):

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

Следующий сценарий возвращает сведения о работоспособности приложения по заданному имени (URI) с указанием фильтров и настраиваемой политики через **System.Fabric.Description.ApplicationHealthQueryDescription**.

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
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности приложения — **Get-ServiceFabricApplicationHealth**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**.

Следующий командлет возвращает сведения о работоспособности приложения fabric:/WordCount:

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

Следующий командлет PowerShell передает настраиваемые политики. Кроме того, он выполняет фильтрацию дочерних элементов и событий.

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Получение сведений о работоспособности службы
В результате возвращаются сведения о работоспособности сущности службы. В них содержатся данные о состоянии работоспособности разделов. Входные данные:

- имя службы (URI), определяющее службу (обязательный параметр);
- политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);
- фильтры событий и разделов, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события и разделы, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности службы через API, создайте FabricClient и вызовите метод **GetServiceHealthAsync** в HealthManager.

В следующем примере возвращаются сведения о работоспособности службы с указанным именем (URI):

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

Следующий сценарий возвращает сведения о работоспособности службы по указанному имени, то есть универсальному коду ресурса (URI) службы с указанием фильтров и настраиваемой политики через System.Fabric.Description.ServiceHealthQueryDescription.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности службы — **Get-ServiceFabricServiceHealth**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**.

Следующий командлет возвращает сведения о работоспособности службы, используя политику работоспособности по умолчанию:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## Получение сведений о работоспособности раздела
Возвращает сведения о работоспособности сущности раздела. В них содержатся данные о состоянии работоспособности реплик. Входные данные:

- идентификатор раздела (GUID), определяющий раздел (обязательный параметр);

- политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);

- фильтры для событий и реплик, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события и реплики, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности раздела через API, создайте FabricClient и вызовите метод **GetPartitionHealthAsync** в HealthManager. Чтобы задать необязательные параметры, создайте **System.Fabric.Description.PartitionHealthQueryDescription**.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности раздела — **Get-ServiceFabricPartitionHealth**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**.

Следующий командлет возвращает сведения о работоспособности для всех разделов службы WordCount.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## Получение сведений о работоспособности реплики
Возвращает сведения о работоспособности реплики. Входные данные:

- идентификатор раздела (GUID) и идентификатор реплики, определяющие реплику (обязательные параметры);

- параметры политики работоспособности приложения, используемые для переопределения политик манифеста приложения (необязательные параметры);

- фильтры для событий, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности реплики через API, создайте FabricClient и вызовите метод **GetReplicaHealthAsync** в HealthManager. Чтобы указать дополнительные параметры, используйте **System.Fabric.Description.ReplicaHealthQueryDescription**.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности реплики — **Get-ServiceFabricReplicaHealth**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**.

Следующий командлет возвращает сведения о работоспособности первичной реплики для всех разделов службы:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Получение сведений о работоспособности развернутого приложения
Возвращает сведения о работоспособности приложения, развернутого на сущности узла. В них содержатся данные о состоянии работоспособности развернутых пакетов служб. Входные данные:

- имя приложения (URI) и имя узла (строка), определяющие развернутое приложение (обязательные параметры);

- политика работоспособности приложения, используемая для переопределения политик манифеста приложения (необязательный параметр);

- фильтры для событий и развернутых пакетов служб, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события и развернутые пакеты служб, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности приложения, развернутого на узле, через API, создайте FabricClient и вызовите метод **GetDeployedApplicationHealthAsync** в HealthManager. Чтобы задать необязательные параметры, используйте **System.Fabric.Description.DeployedApplicationHealthQueryDescription**.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности развернутых приложений — **Get-ServiceFabricDeployedApplicationHealth**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**. Чтобы узнать, где развернуто приложение, выполните командлет **Get-ServiceFabricApplicationHealth** и просмотрите дочерние элементы развернутого приложения.

Следующий командлет возвращает сведения о работоспособности приложения fabric:/WordCount, развернутого на узле Node.1.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Получение сведений о работоспособности развернутого пакета службы
Возвращает сведения о работоспособности сущности развернутого пакета службы. Входные данные:

- имя приложения (URI), имя узла (строка) и имя манифеста служб (строка), определяющие развернутый пакет службы (обязательные параметры);

- политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);

- фильтры для событий, с помощью которых можно указать, какие записи представляют интерес и будут возвращены в результате, например только с ошибками или с предупреждениями и ошибками (необязательные параметры). Обратите внимание, что при оценке сводного состояния работоспособности сущности используются все события, независимо от выбранного фильтра.

### API
Чтобы получить сведения о работоспособности развернутого пакета службы через API, создайте FabricClient и вызовите метод **GetDeployedServicePackageHealthAsync** в HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности развернутого пакета службы — **Get-ServiceFabricDeployedServicePackageHealth**. Сначала подключитесь к кластеру, используя командлет **Connect-ServiceFabricCluster**. Чтобы узнать, где развернуто приложение, выполните командлет **Get-ServiceFabricApplicationHealth** и просмотрите развернутые приложения. Чтобы увидеть пакеты служб в приложении, просмотрите дочерние элементы развернутого пакета службы в выходных данных командлета **Get-ServiceFabricDeployedApplicationHealth**.

Следующий командлет возвращает сведения о работоспособности пакета службы **WordCount.Service** приложения fabric:/WordCount, развернутого на узле Node.1. Эта сущность составляет отчеты **System.Hosting** при успешной активации пакета службы и точки входа, а также при успешной регистрации типа службы.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Общие запросы
Общие запросы возвращают перечень сущностей Service Fabric указанного типа. Их можно просмотреть с помощью API (с помощью методов **FabricClient.QueryManager**), командлетов PowerShell и REST. Эти запросы состоят из вложенных запросов от нескольких компонентов. Один из них — [хранилище данных о работоспособности](service-fabric-health-introduction.md#health-store), заполняющее сводные данные о состоянии работоспособности в каждом результате запроса.

> [AZURE.NOTE] Общие запросы возвращают сводные данные о состоянии работоспособности сущности и не содержат подробных данных о работоспособности. Если сущность неработоспособна, вы можете отправить запросы о работоспособности, чтобы получить всю информацию о работоспособности, в том числе события, состояние работоспособности дочерних элементов и оценку неработоспособности.

Если после выполнения общего запроса возвращается неизвестное состояние, возможно, в хранилище данных о работоспособности нет всех данных о сущности. Но также возможно, что вложенный запрос к хранилищу завершился с ошибкой (например, в результате ошибки связи или регулирования хранилища данных о работоспособности). Отправьте дополнительный запрос о работоспособности сущности. При возникновении временных ошибок вложенных запросов, таких как проблемы с сетью, выполнение следующего запроса может завершиться успешно. В нем также могут содержаться дополнительные сведения из хранилища данных о работоспособности о том, почему не предоставлена сущность.

Ниже приведены запросы, возвращающие сведения о **состоянии работоспособности** для сущностей:

- на список узлов — возвращает список узлов в кластере.
  - API: FabricClient.QueryManager.GetNodeListAsync.
  - PowerShell: Get-ServiceFabricNode.
- на список приложений — возвращает список приложений в кластере.
  - API: FabricClient.QueryManager.GetApplicationListAsync.
  - PowerShell: Get-ServiceFabricApplication.
- на список служб — возвращает список служб в приложении.
  - API: FabricClient.QueryManager.GetServiceListAsync.
  - PowerShell: Get-ServiceFabricService.
- на список разделов — возвращает список разделов в службе.
  - API: FabricClient.QueryManager.GetPartitionListAsync.
  - PowerShell: Get-ServiceFabricPartition.
- на список реплик — возвращает список реплик в разделе.
  - API: FabricClient.QueryManager.GetReplicaListAsync.
  - PowerShell: Get-ServiceFabricReplica.
- на список развернутых приложений — возвращает список развернутых приложений на узле.
  - API: FabricClient.QueryManager.GetDeployedApplicationListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.
- на список развернутых пакетов служб — возвращает список пакетов служб в развернутом приложении.
  - API: FabricClient.QueryManager.GetDeployedServicePackageListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.

### Примеры

Следующий сценарий возвращает неработоспособные приложения в кластере:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Следующий командлет возвращает сведения о приложении fabric:/WordCount. Обратите внимание, что состояние работоспособности — "Предупреждение".

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

Следующий командлет возвращает службы с состоянием работоспособности "Предупреждение":

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Обновление кластера и приложений
Во время отслеживаемого обновления кластера и приложений Service Fabric проверяет все компоненты, чтобы обеспечить их непрерывную работоспособность. Если в результате оценки с использованием настроенных политик работоспособности окажется, что у сущности неработоспособное состояние, при обновлении будут применены соответствующие ему политики, чтобы определить, какое действие должно быть следующим. Обновление можно приостановить, чтобы пользователь мог внести изменения (например, исправить ошибки или изменить политики). Кроме того, можно выполнить автоматический откат до предыдущей рабочей версии.

Во время обновления *кластера* можно узнать его состояние. Эти сведения включают в себя данные об оценках неработоспособности, которые указывают, что не работает в кластере. Если выполняется откат обновления из-за проблем работоспособности, состояние обновления будет содержать последние причины неработоспособности. Это позволяет сохранять сведения, которые помогут администраторам определить, в чем проблема.

Аналогичным образом, это можно сделать во время обновления *приложения*. В состоянии обновления приложения содержатся данные оценки неработоспособности.

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

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Дополнительные сведения см. в статье [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

## Использование оценки работоспособности для устранения неполадок
При возникновении проблемы с кластером или приложением просмотрите сведения о работоспособности кластера или приложения, чтобы выявить неполадку. Данные оценки неработоспособности содержат подробные сведения о причине активации текущего состояния неработоспособности. При необходимости можно детализировать неработоспособные дочерние сущности, чтобы определить основную причину.

## Дальнейшие действия
[Устранение неполадок с помощью отчетов о работоспособности системы](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Добавление настраиваемых отчетов о работоспособности Service Fabric](service-fabric-report-health.md)

[Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->