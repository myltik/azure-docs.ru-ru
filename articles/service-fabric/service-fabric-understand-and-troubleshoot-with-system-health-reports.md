<properties
   pageTitle="Использование отчетов о работоспособности системы для устранения неполадок"
   description="Описание системных отчетов о работоспособности и их использовании для устранения неполадок с кластерами или приложениями"
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
   ms.date="09/03/2015"
   ms.author="oanapl"/>

# Использование отчетов о работоспособности системы для устранения неполадок

Компоненты Service Fabric поддерживают создание отчетов по всем сущностям без дополнительной настройки. В [хранилища данных о работоспособности](service-fabric-health-introduction.md#health-store) сущности создаются и удаляются на основании отчетов системы. Кроме того, эти сущности упорядочиваются в иерархию с учетом взаимодействия между ними.

> [AZURE.NOTE]Узнайте больше о [модели работоспособности Service Fabric](service-fabric-health-introduction.md) и понятиях, связанных с работоспособностью.

В отчетах о работоспособности системы рассматриваются функции кластеров и приложений, а также отмечаются проблемы с работоспособностью. Что качается приложений и служб, отчеты о работоспособности системы проверяют правильность реализации и поведения сущностей с точки зрения платформы Service Fabric. Отчеты не обеспечивают отслеживание работоспособности бизнес-логики службы или обнаружение зависших процессов. Пользовательские службы могут расширить данные о работоспособности сведениями, характерными для их логики.

> [AZURE.NOTE]Отчеты о работоспособности устройств наблюдения отображаются только **после** того, как компоненты системы создают сущность. При удалении сущности хранилище данных о работоспособности автоматически удаляет все связанные с ней отчеты о работоспособности. Это же происходит при создании экземпляра сущности (например, экземпляра реплики службы): все отчеты, связанные со старым экземпляром, удаляются из хранилища.

На отчеты о компонентах системы указывает их источник, который начинается с префикса *System.* Устройства наблюдения не могут использовать тот же префикс в своих источниках, так как отчеты будут отклонены из-за недопустимых параметров. Рассмотрим некоторые системные отчеты, чтобы понять, как они активируются и как устранить возможные проблемы, связанные с ними.

> [AZURE.NOTE]В платформу Service Fabric по-прежнему добавляются отчеты, которые улучшают видимость процессов, выполняемых в кластерах и приложениях.

## Системные отчеты о работоспособности кластеров
В хранилище данных о работоспособности автоматически создается сущность работоспособности кластера, поэтому если все работает правильно, в этом хранилище нет системного отчета.

### Потеря окружения
System.Federation сообщает об ошибке при обнаружении потери окружения. Отчет приходит из отдельных узлов. В имя свойства включается идентификатор узла. Если во всей кольцевой сети Service Fabric имеется одна потеря окружения, обычно может возникнуть два события (отчеты создаются по обе стороны разрыва). При наличии нескольких потерь окружения возникнут дополнительные события. В отчете в качестве срока жизни указывается время ожидания глобальной аренды. Отчет повторно отправляется дважды в течение срока жизни, пока условие актуально. Событие автоматически удаляется после истечения срока действия. Поэтому если узел, с которого отправляется отчет, не работает, его данные по-прежнему должным образом удаляются из хранилища данных о работоспособности.

- SourceId: System.Federation
- Свойство: начинается с "Neighborhood" и включает сведения об узле.
- Дальнейшие действия: изучите причину потери окружения. Например, проверьте связь между узлами кластера.

## Системные отчеты о работоспособности узлов
System.FM, которая представляет из себя службу диспетчера отработки отказов, — это система, которая управляет сведениями об узлах кластера. На любом узле должен быть один отчет системы System.FM о его состояния. Сущности узла удаляются при его отключении.

### Включение и отключение узла
Система System.FM сообщает о нормальном состоянии, если узел присоединяется к кольцевой сети (запускается и работает) и выдает сообщение об ошибке, когда узел отключается от кольцевой сети (не работает из-за обновления или сбоя). Иерархия работоспособности, созданная в хранилище данных о работоспособности, выполняет действия в отношении развернутых сущностей с учетом отчетов об узлах System.FM. Узел считается виртуальным родительским элементом всех развернутых сущностей. Развернутые сущности на этом узле не предоставляются посредством запросов, если узел не работает или не создает отчеты, а также если на узле имеется экземпляр, отличный от экземпляра, связанного с сущностями. Когда система FM сообщает о прекращении работы или перезапуске узла (новый экземпляр), хранилище данных о работоспособности автоматически удаляет развернутые сущности, которые могут существовать только на отключенном узле или предыдущем экземпляре узла.

- SourceId: System.FM
- Свойство: State
- Дальнейшие действия: если узел не работает вследствие обновления, он запустится после обновления. В этом случае для состояния работоспособности снова будет установлено значение ОК. Если узел не возобновляет работу или вышел из строя, необходимо дополнительно проанализировать ситуацию.

Ниже показано событие System.FM с нормальным состоянием работоспособности для работающего узла.

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### Истечение срока действия сертификата
System.FabricNode выдает предупреждение, когда приближается истечение срока действия сертификатов, используемых узлом. Есть три сертификата на узел: Certificate\_cluster, Certificate\_server и Certificate\_default\_client. Если сертификат остается действительным по крайней мере еще в течение двух недель, в отчете сообщается об отсутствии проблем. Если срок действия истекает в течение двух недель, отображается отчет с предупреждением. Срок жизни этих событий неограниченный. Они удаляются при выходе узла из кластера.

- SourceId: System.FabricNode
- Свойство: начинается с "Certificate" и содержит дополнительные сведения о типе сертификата.
- Дальнейшие действия: обновите сертификаты, если их срок действия вскоре истечет.

### Нарушение емкости для загрузки
Подсистема балансировки нагрузки платформы Service Fabric выдает предупреждение, если обнаруживает нарушение емкости узла.

 - SourceId: System.PLB
 - Свойство: начинается с "Capacity".
 - Дальнейшие действия: проверьте предоставленные метрики и просмотрите данные о текущей емкости на узле.

## Системные отчеты о работоспособности приложений
System.CM, которая представляет из себя службу диспетчера кластеров, — это система, которая управляет сведениями о приложении.

### Состояние
Система System.CM выдает сообщает об отсутствии проблем при создании или обновлении приложения. Она извещает хранилище данные о работоспособности при удалении приложения, чтобы обеспечить его удаление из хранилища.

- SourceId: System.CM
- Свойство: State
- Дальнейшие действия: если приложение создано, оно должно включать отчет о работоспособности CM. В противном случае проверьте состояние приложения, выполнив запрос (например, командлет PowerShell Get-ServiceFabricApplication -ApplicationName <applicationName>).

Ниже показано событие State в приложении fabric:/WordCount.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## Системные отчеты о работоспособности служб
System.FM, которая представляет из себя службу диспетчера отработки отказов, — это система, которая управляет сведениями о службах.

### Состояние
Система System.FM сообщает об отсутствии проблем при создании службы. Она удаляет сущность из хранилища данных о работоспособности при удалении службы.

- SourceId: System.FM
- Свойство: State

Ниже показано событие State в службе fabric:/WordCount/WordCountService.

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### Нарушение: неразмещенные реплики
Система System.PLB выдает предупреждение, если ей не удалось найти расположение для одной или нескольких реплик службы. Отчет удаляется по истечении его срока действия.

- SourceId: System.FM
- Свойство: State
- Дальнейшие действия: проверьте ограничения службы. Проверьте текущее состояние запроса.

## Системные отчеты о работоспособности разделов
System.FM, которая представляет из себя службу диспетчера отработки отказов, — это система, которая управляет сведениями о разделах служб.

### Состояние
Система System.FM сообщает об отсутствии проблем при создании работоспособного раздела. Она удаляет сущность из хранилища данных о работоспособности при удалении раздела. Если в разделе нет минимального требуемого количества реплик, она сообщает об ошибке. Если в разделе есть минимальное требуемое количество реплик, но оно меньше целевого числа реплик, она выводит предупреждение. Если в разделе наблюдается потеря кворума, система FM сообщает об ошибке. Другие важные события: предупреждение о том, что перенастройка или сборка заняли больше времени, чем ожидалось. Ожидаемое время, необходимое для сборки или перенастройки, настраивается на основе сценариев службы. Например, если в службе (например, SQL Azure) есть данные о состоянии объемом в несколько терабайт, сборка займет больше времени, чем в случае службы с небольшим объемом данных о состоянии.

- SourceId: System.FM
- Свойство: State
- Дальнейшие действия: если с состоянием работоспособности наблюдаются проблемы, возможно, некоторые реплики не созданы или не открыты, либо их уровень не повышен до первичной или вторичной реплики должным образом. Во многих случаях основной причиной является ошибка службы при открытии или изменении реализации роли.

Ниже показан работоспособный раздел.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

Ниже показаны данные о работоспособности раздела, количество реплик в котором меньше целевого значения. Дальнейшие действия: получите описание раздела со сведениями о его конфигурации: MinReplicaSetSize равно 2, а TargetReplicaSetSize — 7. Затем получите данные о количестве узлов в кластере: 5. Поэтому в этом случае невозможно разместить 2 реплики.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### Нарушение ограничений в отношении реплик
Система System.PLB выдает предупреждение, если обнаруживает нарушение ограничений в отношении реплик и не может разместить реплики раздела.

- SourceId: System.PLB
- Свойство: начинается с ReplicaConstraintViolation.

## Системные отчеты о работоспособности реплик
System.RA, которая представляет из себя компонент агента перенастройки, — это система для отслеживания состояния реплики.

### Состояние
Система System.RA сообщает об отсутствии проблем при создании реплики.

- SourceId: System.RA
- Свойство: State

Ниже показана работоспособная реплика.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### Состояние открытия реплики
Описание этого отчета о работоспособности содержит время начала (в формате UTC) при вызове API. Система System.RA выдает предупреждение, если реплика открывается дольше заданного периода (по умолчанию: 30 минут). Если интерфейс API влияет на доступность службы, отчет выполняется гораздо быстрее (с заданным интервалом, который по умолчанию равен 30 секундам). Сюда входит время, необходимое для открытия репликатора и службы. Свойство меняется на ОК при открытии.

- SourceId: System.RA
- Свойство: ReplicaOpenStatus
- Дальнейшие действия: если с состоянием работоспособности наблюдаются проблемы, проверьте, почему реплика открывается дольше, чем ожидалось.

### Медленный вызов API службы
Системы System.RAP и System.Replicator выдают предупреждение, если вызов пользовательского кода службы длится больше заданного периода. Предупреждение удаляется по завершении вызова.

- SourceId: System.RAP или System.Replicator
- Свойство: имя медленного API В описании приводятся дополнительные сведения об API, в работе которого возникла задержка.
- Дальнейшие действия: изучите причину, по которой вызов занимает больше времени, чем ожидалось.

В следующем примере показан раздел с потерей кворума и приведены действия, позволяющие определить причину проблемы. Для одной из реплик получено предупреждение о состоянии работоспособности, поэтому мы получаем данные о ее работоспособности. Они свидетельствуют, что выполнение операций службы занимает больше времени, чем ожидалось (об этом событии сообщает система System.RAP). После получения этих сведений необходимо рассмотреть и изучить код службы. В этом случае реализация RunAsync службы с отслеживанием состояния вызывает необработанное исключение. Обратите внимание, что реплики перезапускаются, поэтому может не отображаться ни одной реплики с состоянием предупреждения. Снова получите данные о работоспособности и посмотрите, имеются ли различия в идентификаторе реплики. В некоторых случаях это помогает.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

При запуске неисправного приложения с помощью отладчика исключение, выданное RunAsync, отображается в окне "События диагностики":

![События диагностики Visual Studio 2015: сбой RunAsync в fabric:/HelloWorldStatefulApplication.][1]

События диагностики Visual Studio 2015: сбой RunAsync в fabric:/HelloWorldStatefulApplication.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### Переполнение очереди репликации
Система System.Replicator выдает предупреждение при переполнении очереди репликации. На сервере-источника это обычно происходит, потому что одна или несколько вторичных реплик выполняются медленно для подтверждения операций. На сервере-получателе это обычно происходит, если служба медленно применяет операции. Предупреждение удаляется при очистке очереди.

- SourceId: System.Replicator
- Свойство: PrimaryReplicationQueueStatus или SecondaryReplicationQueueStatus в зависимости от роли реплики

## Системные отчеты о работоспособности DeployedApplication
System.Hosting — это система для работы с развернутыми сущностями.

### Активация
Система System.Hosting сообщает об отсутствии проблем, если приложение успешно активировано на узле. В противном случае отображается сообщение об ошибке.

- SourceId: System.Hosting
- Свойство: Activation Включает версию выпуска.
- Дальнейшие действия: при наличии проблем с работоспособностью исследуйте причину ошибки при активации.

Ниже приведен пример успешной активации.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Скачивание
Система System.Hosting сообщает об ошибке при скачивании пакета приложения.

- SourceId: System.Hosting
- Свойство: Download:<RolloutVersion>
- Дальнейшие действия: изучите причину ошибки при скачивании на узле.

## Системные отчеты о работоспособности DeployedServicePackage
System.Hosting — это система для работы с развернутыми сущностями.

### Активация пакета службы
Система System.Hosting сообщает об отсутствии проблем, если пакет службы на узле успешно активирован. В противном случае отображается сообщение об ошибке.

- SourceId: System.Hosting
- Свойство: Activation
- Дальнейшие действия: исследуйте причину ошибки при активации.

### Активация пакета кода
System.Hosting сообщает об отсутствии проблем, если каждый код пакета успешно активирован. Если активация завершилась с ошибкой, выводится заданное предупреждение. Если не удалось активировать CodePackage, или если этот элемент завершил работу с ошибкой и превышением значения настроенного параметра "CodePackageHealthErrorThreshold", система выдает сообщение об ошибке. Если пакет службы включает несколько пакетов кода, для каждого из них создается отчет об активации.

- SourceId: System.Hosting
- Свойство: префикс CodePackageActivation, имя пакета кода и точка входа CodePackageActivation:<CodePackageName>:<SetupEntryPoint/EntryPoint>. Например, CodePackageActivation:Code:SetupEntryPoint.

### Регистрация типа службы
Система System.Hosting сообщает об отсутствии проблем, если тип службы успешно зарегистрирован. Система выдает сообщение об ошибке, если регистрация не выполнена вовремя (настраивается с помощью параметра ServiceTypeRegistrationTimeout). Если тип службы не регистрируется на узле из-за закрытия среды выполнения, система Hosting выдает предупреждение.

- SourceId: System.Hosting
- Свойство: префикс ServiceTypeRegistration и имя типа службы Например, ServiceTypeRegistration:FileStoreServiceType

Ниже приведен пример работоспособного развернутого пакета службы.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Скачивание
Система System.Hosting сообщает об ошибке при скачивании пакета службы.

- SourceId: System.Hosting
- Свойство: Download:<RolloutVersion>
- Дальнейшие действия: изучите причину ошибки при скачивании на узле.

### Проверка обновления
System.Hosting сообщает об ошибке проверки во время обновления или об ошибке обновления на узле.

- SourceId: System.Hosting
- Свойство: префикс FabricUpgradeValidation, версия обновления
- Описание: сведения о возникшей ошибке

## Дальнейшие действия
[Просмотр отчетов о работоспособности Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Локальный мониторинг и диагностика состояния служб](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Oct15_HO3-->