<properties
   pageTitle="Просмотр сводных данных о работоспособности сущностей Azure Service Fabric"
   description="Описание процедуры отправки запросов, просмотра и оценки сводных данных о работоспособности сущностей Azure Service Fabric при помощи запросов о работоспособности и общих запросов."
   services="service-fabric"
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
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# Просмотр отчетов о работоспособности Service Fabric
В Service Fabric представлена [модель работоспособности](service-fabric-health-introduction.md), состоящая из сущностей работоспособности, для которых системные компоненты и модули наблюдения могут создавать отчеты по отслеживаемым локальным условиям. [Хранилище данных о работоспособности](service-fabric-health-introduction.md#health-store) содержит все данные о работоспособности, чтобы определить степень работоспособности сущностей.

По умолчанию кластер заполняется отчетами о работоспособности, отправляемыми системными компонентами. Подробнее об этом читайте в статье [Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

В Service Fabric доступны несколько способов получения сводных данных о работоспособности сущностей:

- [обозреватель Service Fabric](service-fabric-visualizing-your-cluster.md) или другие средства визуализации;

- запросы о работоспособности (с помощью PowerShell, API или REST);

- общие запросы, возвращающие перечень сущностей, среди свойств которых числится работоспособность (с помощью PowerShell, API или REST).

Для демонстрации этих параметров мы будем использовать локальный кластер с **5 узлами**. Рядом с приложением **fabric:/System** (присутствующим по умолчанию) расположены некоторые другие развернутые приложения, одно из которых — **fabric:/WordCount**. Это приложение содержит службу с отслеживанием состояния, настроенную на семь реплик. Так как доступно лишь пять узлов, компоненты системы отобразят предупреждение о том, что количество реплик в разделе ниже требуемого.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Работоспособность в обозревателе Service Fabric
Обозреватель Service Fabric предоставляет визуальное представление кластера. На рисунке ниже видно следующее:

- Приложение **fabric:/WordCount** выделено красным (ошибка), поскольку служба MyWatchdog вывела сообщение об ошибке при проверке свойства "Availability" (Доступность).

- Одна из его служб, **fabric:/WordCount/WordCount.Service**, выделена желтым (предупреждение). Как описано выше, служба настроена на семь реплик, которые невозможно разместить полностью (так как доступно лишь пять узлов). Раздел службы выделен желтым из-за отчета системы, хотя здесь это не показано. Раздел желтого цвета приводит к выделению службы тем же цветом.

- **Кластер** выделен красным, поскольку приложение выделено тем же цветом.

При оценке используются политики по умолчанию из манифеста кластера и манифеста приложения, являющиеся строгими (т. е. не допускают сбоев).

![Представление кластера в обозревателе Service Fabric.][1]

Представление кластера в обозревателе Service Fabric.

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]Дополнительные сведения об [обозревателе Service Fabric](service-fabric-visualizing-your-cluster.md).

## Запросы о работоспособности
Service Fabric предоставляет запросы о работоспособности по каждому поддерживаемому [типу сущностей](service-fabric-health-introduction.md#health-entities-and-hierarchy). Их можно просмотреть с помощью API (методы на FabricClient.HealthManager), командлетов PowerShell и REST. Эти запросы возвращают полную информацию о работоспособности сущности, включая сводное состояние работоспособности, события работоспособности, связанные с сущностью, состояние работоспособности дочерних элементов (в соответствующих случаях) и оценки неработоспособности, если сущность неработоспособна.

> [AZURE.NOTE]Сущность работоспособности возвращается пользователю после ее полного заполнения в хранилище данных о работоспособности, то есть для нее создан отчет системы, она активна (не удалена), а для родительских сущностей в цепи иерархии также созданы отчеты системы. Если любое из этих условий не выполнено, запросы о работоспособности возвращают исключение с указанием причины, по которой сущность не может быть возвращена.

Для запросов о работоспособности требуется передача идентификатора сущности, зависящего от типа сущности. Они принимают необязательные параметры политики работоспособности. Если не указано иное, при оценке используются [политики работоспособности](service-fabric-health-introduction.md#health-policies) из манифеста кластера или приложения. Они также принимают фильтры для возврата только частичных сведений о дочерних элементах или событий, соответствующих условиям указанных фильтров.

> [AZURE.NOTE]На стороне сервера применяются выходные фильтры для сокращения размера ответных сообщений. Рекомендуем использовать фильтры для ограничения объема возвращаемых данных, вместо того чтобы применять фильтры на стороне клиента.

Работоспособность сущности содержит следующую информацию:

- Сводное состояние работоспособности сущности. Эти данные вычисляются хранилищем данных о работоспособности на основании отчетов о работоспособности сущности, состояния работоспособности дочерних элементов (в соответствующих случаях) и политик работоспособности. Дополнительные сведения об [оценке работоспособности сущности](service-fabric-health-introduction.md#entity-health-evaluation).  

- События работоспособности, связанные с сущностью.

- Для сущностей, у которых могут быть дочерние элементы, — коллекция состояний работоспособности для всех дочерних элементов. Каждое состояние работоспособности содержит идентификатор сущности и сводное состояние работоспособности. Чтобы получить полную информацию о работоспособности дочернего элемента, вызовите запрос о работоспособности для типа дочерней сущности, передав идентификатор дочернего элемента.

- Если сущность неработоспособна, — оценки неработоспособности, указывающие на отчет, вызвавший такое состояние сущности.

## Получение сведений о работоспособности кластера
Возвращает сведения о работоспособности сущности кластера. Содержит данные о состоянии работоспособности приложений и узлов (дочерних элементов кластера). Входные данные:

- сопоставление политики работоспособности приложения с политиками работоспособности, которое используется для переопределения политик манифеста приложения (необязательный параметр);

- фильтр для возврата только событий, узлов и приложений с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности кластера, создайте FabricClient и вызовите метод GetClusterHealthAsync в HealthManager.

Следующий сценарий возвращает сведения о работоспособности кластера:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

Следующий сценарий возвращает сведения о работоспособности кластера с помощью настраиваемой политики работоспособности кластера и фильтров для узлов и приложений. Обратите внимание, что он создает элемент System.Fabric.Description.ClusterHealthQueryDescription, содержащий все входные данные.

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
Командлет для получения сведений о работоспособности кластера — Get-ServiceFabricClusterHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster. Состояние кластера: 5 узлов, приложение системы и fabric:/WordCount с указанными выше настройками.

Следующий командлет возвращает сведения о работоспособности кластера с помощью политик работоспособности по умолчанию. Сводное состояние работоспособности — "Warning", поскольку приложение fabric:/WordCount находится в таком же состоянии. Обратите внимание, что оценки неработоспособности отображаются с подробными сведениями об условии, вызвавшем такое сводное состояние.

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

Следующие командлет PowerShell возвращает сведения о работоспособности кластера с помощью настраиваемой политики приложения. Он фильтрует результаты для возврата только приложений и узлов с состоянием "Error" или "Warning". В результате ни один узел не будет возвращен, поскольку они все работоспособны. Только приложение fabric:/WordCount соответствует условиям фильтра приложения. Поскольку по условиям настраиваемой политики предупреждение считается ошибкой для приложения fabric:/WordCount, состояние приложения, как и кластера, оценивается как "Error".

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
Возвращает сведения о работоспособности сущности узла. Содержит сведения о событиях работоспособности, связанных с узлом. Входные данные:

- имя узла, определяющее узел (обязательный параметр);

- параметры политики работоспособности кластера, используемые для оценки работоспособности (необязательный параметр);

- фильтр для возврата только событий с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности узла через API, создайте FabricClient и вызовите метод GetNodeHealthAsync в HealthManager.

Следующий сценарий возвращает сведения о работоспособности узла по указанному имени узла.

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

Следующий сценарий возвращает сведения о работоспособности узла по указанному имени узла, передавая фильтр событий и настраиваемую политику через System.Fabric.Description.NodeHealthQueryDescription.

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности узла — Get-ServiceFabricNodeHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster. Следующий командлет возвращает сведения о работоспособности узла с помощью политик работоспособности по умолчанию.

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

Следующий командлет возвращает сведения о работоспособности всех узлов в кластере.

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
Возвращает сведения о работоспособности сущности приложения. Содержит сведения о состоянии работоспособности развернутого приложения и дочерних элементов службы. Входные данные:

- имя приложения (URI), определяющее приложение (обязательный параметр);

- политика работоспособности приложения, используемая для переопределения политик манифеста приложения (необязательный параметр);

- фильтр для возврата только событий, служб и развернутых приложений с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности приложения, создайте FabricClient и вызовите метод GetApplicationHealthAsync в HealthManager.

Следующий сценарий возвращает сведения о работоспособности приложения по указанному имени, то есть универсальному коду ресурса (URI) приложения.

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

Следующий сценарий возвращает сведения о работоспособности приложения по указанному имени, то есть универсальному коду ресурса (URI) приложения с указанием фильтров и настраиваемой политики через System.Fabric.Description.ApplicationHealthQueryDescription.

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
Командлет для получения сведений о работоспособности приложения — Get-ServiceFabricApplicationHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster.

Следующий командлет возвращает сведения о работоспособности приложения fabric:/WordCount.

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

Следующий командлет PowerShell передает настраиваемую политику и фильтрует дочерние элементы и события.

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
Возвращает сведения о работоспособности сущности службы. Содержит сведения о состоянии работоспособности разделов. Входные данные:

- имя службы (URI), определяющее службу (обязательный параметр);
- политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);
- фильтр для возврата только событий и разделов с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности службы через API, создайте FabricClient и вызовите метод GetServiceHealthAsync в HealthManager.

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
Командлет для получения сведений о работоспособности службы — Get-ServiceFabricServiceHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster.

Следующий командлет возвращает сведения о работоспособности службы с помощью политик работоспособности по умолчанию.

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
Возвращает сведения о работоспособности сущности раздела. Содержит сведения о состоянии работоспособности реплик. Входные данные:

- идентификатор раздела (GUID), определяющий раздел (обязательный параметр);

- политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);

- фильтр для возврата только событий и реплик с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности раздела через API, создайте FabricClient и вызовите метод GetPartitionHealthAsync в HealthManager. Чтобы задать необязательные параметры, создайте элемент System.Fabric.Description.PartitionHealthQueryDescription.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности раздела — Get-ServiceFabricPartitionHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster.

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

- идентификатор раздела (GUID) и идентификатор реплики, определяющие реплику (обязательный параметр);

- параметры политики работоспособности приложения, используемые для переопределения политик манифеста приложения (необязательный параметр);

- фильтр для возврата только событий с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности реплики через API, создайте FabricClient и вызовите метод GetReplicaHealthAsync в HealthManager. Укажите дополнительные параметры с помощью System.Fabric.Description.ReplicaHealthQueryDescription.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности реплики — Get-ServiceFabricReplicaHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster.

Следующий командлет возвращает сведения о работоспособности основной реплики для всех разделов службы.

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
Возвращает сведения о работоспособности приложения, развернутого на сущности узла. Содержит сведения о состоянии работоспособности развернутого пакета службы. Входные данные:

- имя приложения (URI) и имя узла (string), определяющие развернутое приложение (обязательный параметр);

- политика работоспособности приложения, используемая для переопределения политик манифеста приложения (необязательный параметр);

- фильтр для возврата только событий и развернутых пакетов служб с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности приложения, развернутого на узле, через API, создайте FabricClient вызовите метод GetDeployedApplicationHealthAsync в HealthManager. Чтобы задать необязательные параметры, используйте элемент System.Fabric.Description.DeployedApplicationHealthQueryDescription.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности развернутых приложений — Get-ServiceFabricDeployedApplicationHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster. Чтобы узнать, где развернуто приложение, выполните командлет Get-ServiceFabricApplicationHealth и просмотрите дочерние элементы развернутого приложения.

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

- имя приложения (URI), имя узла (string) и имя манифеста службы (string), определяющие развернутый пакет службы (обязательный параметр);

- политика работоспособности приложения, используемая для переопределения политики манифеста приложения (необязательный параметр);

- фильтр для возврата только событий с определенным состоянием работоспособности (например, лишь с ошибками, лишь с предупреждениями или ошибками и т. п.) (необязательный параметр).

### API
Чтобы получить сведения о работоспособности развернутого пакета службы через API, создайте FabricClient вызовите метод GetDeployedServicePackageHealthAsync в HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
Командлет для получения сведений о работоспособности развернутого пакета службы — Get-ServiceFabricDeployedServicePackageHealth. Сначала подключитесь к кластеру при помощи командлета Connect-ServiceFabricCluster. Чтобы увидеть, где развернуто приложение, выполните командлет Get-ServiceFabricApplicationHealth и просмотрите развернутые приложения. Чтобы увидеть пакеты службы в приложении, просмотрите дочерние элементы развернутого пакета службы в выходных данных командлета Get-ServiceFabricDeployedApplicationHealth.

Следующий командлет возвращает сведения о работоспособности пакета службы WordCount.Service приложения fabric:/WordCount, развернутого на узле Node.1. Эта сущность составляет отчеты System.Hosting при успешной активации пакета службы и точки входа, а также при успешной регистрации типа службы.

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
Общие запросы возвращают перечень сущностей Service Fabric указанного типа. Их можно просмотреть с помощью API (методы на FabricClient.QueryManager), командлетов PowerShell и REST. Эти запросы собирают вложенные запросы от нескольких компонентов. Один из них — [хранилище данных о работоспособности](service-fabric-health-introduction.md#health-store), заполняющее сводные данные о состоянии работоспособности в каждом результате запроса.

> [AZURE.NOTE]Общие запросы возвращают сводные данные о состоянии работоспособности сущности и не содержат подробных данных о работоспособности. Если сущность неработоспособна, вы можете отправить запросы о работоспособности, чтобы получить всю информацию о работоспособности, такую как события, состояние работоспособности дочерних элементов и оценки неработоспособности.

Если общие запросы возвращают состояние работоспособности сущности "Unknown" (Неизвестно), то возможно, что в хранилище данных о работоспособности нет всех данных о сущности, либо вложенный запрос к хранилищу завершился с ошибкой (например, в результате ошибки связи, регулирования хранилища данных о работоспособности и т. д.). Отправьте дополнительный запрос о работоспособности сущности. Он может завершиться успешно, если при отправке вложенного запроса возникли кратковременные неполадки (например, сбой в сети), либо предоставит дополнительные сведения о причинах отсутствия доступа к сущности в хранилище данных о работоспособности.

Ниже приведены запросы, возвращающие сведения о состоянии работоспособности для сущностей.

- Список узлов. Возвращает список узлов в кластере.
  - API: FabricClient.QueryManager.GetNodeListAsync.
  - PowerShell: Get-ServiceFabricNode.
- Список приложений. Возвращает список приложений в кластере.
  - API: FabricClient.QueryManager.GetApplicationListAsync.
  - PowerShell: Get-ServiceFabricApplication.
- Список служб. Возвращает список служб в приложении.
  - API: FabricClient.QueryManager.GetServiceListAsync.
  - PowerShell: Get-ServiceFabricService.
- Список разделов. Возвращает список разделов в службе.
  - API: FabricClient.QueryManager.GetPartitionListAsync.
  - PowerShell: Get-ServiceFabricPartition.
- Список реплик. Возвращает список реплик в разделе.
  - API: FabricClient.QueryManager.GetReplicaListAsync.
  - PowerShell: Get-ServiceFabricReplica.
- Список развернутых приложений. Возвращает список развернутых приложений на узле.
  - API: FabricClient.QueryManager.GetDeployedApplicationListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.
- Список развернутых пакетов служб. Возвращает список пакетов служб в развернутом приложении.
  - API: FabricClient.QueryManager.GetDeployedServicePackageListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.

### Примеры

Следующий сценарий возвращает неработоспособные приложения в кластере:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

Следующий командлет возвращает сведения о приложении fabric:/WordCount. Обратите внимание, что состояние работоспособности — "Warning".

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

Следующий командлет возвращает службы с состоянием работоспособности "Warning".

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
Во время отслеживаемого обновления кластера и приложений Service Fabric проверяет все компоненты, чтобы обеспечить их непрерывную работоспособность. Если какой-либо компонент неработоспособен по условиям текущей политики, обновление либо приостанавливается, чтобы пользователь мог внести изменения, либо выполняется его автоматический откат.

Во время обновления **кластера** вы можете получить состояние обновления кластера, включающее оценки неработоспособности, которые указывают на неработоспособный компонент кластера. Если выполняется откат обновления из-за проблем работоспособности, состояние обновления будет содержать последние причины неработоспособности, чтобы администраторы могли выявить проблему. Аналогичным образом во время обновления **приложения** состояние обновления приложения содержит оценки неработоспособности.

Далее показано состояние обновления для измененного приложения fabric:/WordCount. Модуль наблюдения сообщил об ошибке на одной из реплик. Выполняется откат обновления из-за несоответствия условиям проверок работоспособности.

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

Дополнительные сведения об [Обновлении приложения Service Fabric](service-fabric-application-upgrade.md).

## Устранение неполадок с работоспособностью
При возникновении проблемы в кластере или приложении, просмотрите сведения о работоспособности кластера или приложения, чтобы выявить неполадку. Оценки неработоспособности содержат подробную информацию о причине возникновения текущего состояния неработоспособности. При необходимости вы можете более подробно изучить неработоспособные сущности дочерних элементов, чтобы обнаружить источник проблемы.

## Дальнейшие действия
[Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Добавление настраиваемых отчетов о работоспособности Service Fabric](service-fabric-report-health.md)

[Локальный мониторинг и диагностика состояния служб](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=July15_HO4-->