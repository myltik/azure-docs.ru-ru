<properties
   pageTitle="Устранение неполадок с обновлением приложений | Microsoft Azure"
   description="В этой статье рассматриваются некоторые общие проблемы, связанные с обновлением приложений структуры служб, и способы их устранения."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/18/2016"
   ms.author="subramar"/>

# Устранение неполадок при обновлениях приложений

В этой статье рассматриваются некоторые общие проблемы, связанные с обновлением приложений Azure Service Fabric, и способы их устранения.

## Устранение неполадок при неудачном обновлении приложения

При сбое обновления выходные данные команды **Get-ServiceFabricApplicationUpgrade** будут содержать некоторые дополнительные сведения для отладки отказа. Эти сведения могут использоваться для решения следующих задач:

1. Определение типа ошибки.
2. Определение причины ошибки.
3. Локализация отказавших компонентов для дальнейшего исследования.

Эти сведения будут доступны сразу же после того, как Service Fabric обнаружит сбой, независимо от того, будет ли в качестве действия **FailureAction** выполнен откат или приостановка обновления.

### Определение типа ошибки

В выходных данных **Get-ServiceFabricApplicationUpgrade** **FailureTimestampUtc** определяет временную метку (по всеобщему скоординированному времени), при которой сбой обновления был обнаружен Service Fabric и было запущено действие **FailureAction**. **FailureReason** указывает на одну из трех потенциальных высокоуровневых причин сбоя:

1. UpgradeDomainTimeout указывает, что для определенного домена обновление занимает слишком много времени, что привело к истечению срока **UpgradeDomainTimeout**.
2. OverallUpgradeTimeout указывает, что обновление в целом заняло слишком много времени, что привело к истечению срока **UpgradeTimeout**.
3. HealthCheck указывает, что после обновления домена обновления приложение осталось неработоспособным в соответствии с конкретными политиками работоспособности, что привело к истечению времени **HealthCheckRetryTimeout**.

Эти записи будут отображаться в выходных данных только при сбое обновления и выполнении отката. Дальнейшие сведения будут отображаться в зависимости от типа сбоя.

### Исследование времени ожидания обновления

Сбои, связанные с истечением времени ожидания, чаще всего вызваны проблемами с доступностью служб. Выходные данные, приведенные ниже, характерны для обновлений, в которых реплики или экземпляры служб не удается запустить из-за новой версии кода. Поле **UpgradeDomainProgressAtFailure** выполняет моментальный снимок любого ожидаемого обновления в момент сбоя.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

В этом примере мы видим, что при обновлении произошел сбой на уровне домена обновления *MYUD1*, и двум разделам (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* и *4b43f4d8-b26b-424e-9307-7a7a62e79750*) не удалось разместить главные реплики (*WaitForPrimaryPlacement*) на целевых узлах *Node1* и *Node4*.

Команда **Get ServiceFabricNode** может использоваться для проверки нахождения этих двух узлов в домене обновления *MYUD1*. Параметр *UpgradePhase* имеет значение *PostUpgradeSafetyCheck*, что означает, что эти проверки безопасности выполняются после обновления всех узлов в домене обновления. Все эти сведения вместе указывают на потенциальную проблему в новой версии кода приложения. Наиболее обычные неполадки представляют собой явные ошибки службы или ссылки на части основного кода.

Если параметр *UpgradePhase* имеет значение *PreUpgradeSafetyCheck*, то это значит, что при подготовке домена обновления перед началом самого обновления были обнаружены неполадки. Наиболее распространенные неполадки в этом случае представляют собой явные ошибки службы или отклонение от путей основного кода.

Текущее состояние **UpgradeState** имеет значение *RollingBackCompleted*, поэтому исходное обновление могло быть выполнено с использованием действия **FailureAction**, соответствующего откату, когда при сбое выполняется автоматический откат. Если исходное обновление было выполнено с использованием осуществляемого вручную действия **FailureAction**, то обновление вместо этого будет находиться в отложенном состоянии для выполнения динамической отладки приложения.

### Исследование ошибок проверки работоспособности

Сбои проверки работоспособности могут вызываться набором дополнительных ошибок, которые могут возникать после того, как обновление будет завершено во всех узлах в домене обновления при успешном выполнении всех проверок безопасности. Представленные ниже выходные данные характерны для сбоев при обновлении из-за отказавших проверок безопасности. Поле **UnhealthyEvaluations** создает моментальный снимок всех отказавших проверок работоспособности в момент возникновения сбоя в соответствии с заданной пользователем [политикой работоспособности](service-fabric-health-introduction.md).

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Исследование ошибок проверки работоспособности требует понимания модели работоспособности Service Fabric. Но даже без такого глубокого понимания можно увидеть, что две службы не прошли проверку работоспособности, *fabric:/DemoApp/Svc3* и *fabric:/DemoApp/Svc2*, а также имеются ошибочные отчеты проверки работоспособности (в данном случае InjectedFault). В этом примере две из четырех служб неработоспособны, что выше целевого показателя по умолчанию в 0 % неработоспособных служб (*MaxPercentUnhealthyServices*).

Обновление было отложено из-за сбоя, для которого было задано действие **FailureAction**, выполняемое вручную при запуске обновления, поэтому мы можем исследовать потоковую передачу в неисправном состоянии перед выполнением дальнейших действий, если это требуется.

### Восстановление при приостановке обновления

С действием **FailureAction**, для которого назначен откат, восстановление не требуется, поскольку при сбое обновления откат будет выполнен автоматически. При установке действия **FailureAction**, выполняемого вручную, существует несколько вариантов восстановления.

1. Запуск отката вручную
2. Выполнение дальнейших этапов обновления вручную
3. Возобновление отслеживаемых обновлений

Команда **Start-ServiceFabricApplicationRollback** может использоваться в любое время для запуска отката приложения. После того успешного возврата команды запрос отката регистрируется в системе для последующего выполнения.

Команду **Resume-ServiceFabricApplicationUpgrade** можно использовать для выполнения дальнейших этапов обновления вручную последовательно, домен за доменом. В этом режиме системой могут выполняться только проверки безопасности. Никаких дополнительных проверок работоспособности выполняться не будет. Эту команду можно использовать, когда параметр *UpgradeState* приобретет значение *RollingForwardPending*, что означает, что текущий домен обновления завершил обновление, но обновление второго домена еще не началось (ожидание).

Команда **Update-ServiceFabricApplicationUpgrade** может использоваться для возобновления отслеживаемого обновления с проверкой безопасности и работоспособности.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

Обновление будет продолжено с домена обновления, на котором оно было приостановлено последним, с использованием тех же параметров обновления и политик проверки работоспособности, что и раньше. Если это необходимо, любые из параметров обновления и политик проверки работоспособности, отображенных в выходных данных выше, могут быть изменены в той же команде, что и при возобновлении обновления. В этом примере обновление было возобновлено в режиме отслеживания без изменения других параметров, то есть с применением тех же параметров и политик работоспособности, что и ранее.

## Дальнейшее устранение неполадок

### Service Fabric не следует указанным политикам работоспособности

Возможная причина 1.

Структура службы преобразует все процентные значения в фактическое количество сущностей (например, реплик, разделов и служб) для оценки работоспособности с округлением до ближайшего числа целых сущностей. Например, если максимальное значение _MaxPercentUnhealthyReplicasPerPartition_ равно 21 % и имеется пять реплик, то Service Fabric допустит выделение до двух неработоспособных реплик (т. е. `Math.Ceiling (5*0.21)`) при оценке работоспособности раздела. Политики работоспособности должны устанавливаться с учетом этого.

Возможная причина 2.

Политики работоспособности указываются в процентах от общего числа служб, а не в конкретных экземплярах служб. Например, перед обновлением предположим, что приложение имеет четыре экземпляра службы, A, B, C и D, где служба D неработоспособна, однако не имеет серьезного влияния на работу приложения. Мы собираемся пропустить известную неработоспособную службу D во время обновления и установить параметр *MaxPercentUnhealthyServices* в значение 25 %, предполагая, что только службы A, B и C должны оставаться работоспособными.

Однако во время обновления служба D может стать работоспособной, а служба C — потерять работоспособность. В этом случае обновление все-таки завершится успешно, поскольку всего 25 % служб неработоспособно, что может стать причиной неожиданных ошибок из-за того, что служба C неожиданно стала неработоспособной вместо службы D. В такой ситуации службу D необходимо смоделировать как тип службы, отличный от типа служб A, B и C. Поскольку политики работоспособности могут задаваться по каждой службе, этот подход позволяет применить другие пороговые процентные значения для различных служб в зависимости от их роли в приложении.

### Политики работоспособности при обновлении приложения не указаны, однако обновление все равно завершается со сбоем из-за не заданных значений времени ожидания.

Если политики работоспособности не переданы в запросе на обновление, они берутся из файла *ApplicationManifest.xml* текущей версии приложения. Например, при обновлении приложения X с версии v1 до версии v2 используются политики работоспособности приложения, указанные для приложения X версии v1. Если для обновления должна использоваться другая политика работоспособности, то политику будет необходимо задать как часть вызова API обновления приложения. Обратите внимание, что политики, заданные как часть вызова API, применяются только во время обновления. После завершения обновления будут использоваться политики, заданные в файле *ApplicationManifest.xml*.

### Указаны неверные значения времени ожидания

У пользователей могут возникать вопросы о том, что происходит в случае установки несогласованных значений времени ожидания, например, когда значение *UpgradeTimeout* меньше значения *UpgradeDomainTimeout*. В этом случае будет возвращена ошибка. Это может произойти в других случаях, когда значение *UpgradeDomainTimeout* меньше суммы значений параметров *HealthCheckWaitDuration* и *HealthCheckRetryTimeout* или когда значение *UpgradeDomainTimeout* меньше суммы значений *HealthCheckWaitDuration* и *HealthCheckStableDuration*.

### Мои обновления выполняются слишком долго

Время, необходимое для завершения обновления, зависит от различных проверок работоспособности и указанных значений времени ожидания, которые, в свою очередь, зависят от времени, необходимого для обновления приложения (включая копирование пакета, развертывание и стабилизацию). Слишком жесткое назначение времени ожидания может привести к большему числу сбоев при обновлении, поэтому при назначении значений времени ожидания рекомендуется консервативный подход.

Напомним о том, как значения времени ожидания сказываются на времени применения обновлений.

Обновление для домена обновления не может завершиться быстрее, чем сумма значений параметров *HealthCheckWaitDuration* и *HealthCheckStableDuration*.

Сбой обновления не может произойти быстрее, чем сумма значений *HealthCheckWaitDuration* и *HealthCheckRetryTimeout*.

Время обновления для домена обновления ограничено значением *UpgradeDomainTimeout*. Если значения *HealthCheckRetryTimeout* и *HealthCheckStableDuration* оба не равны нулю, а работоспособность приложения постоянно меняется, то время ожидания обновления обязательно завершится при достижении значения *UpgradeDomainTimeout*. *UpgradeDomainTimeout* начинает обратный отсчет с момента начала текущего домена обновления.

## Дальнейшие действия

Пошаговое руководство [Обновление приложения с помощью Visual Studio](service-fabric-application-upgrade-tutorial.md) поможет вам выполнить обновление приложения с помощью Visual Studio.

Пошаговое руководство [Обновление приложения с помощью PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) поможет вам выполнить обновление приложения с помощью PowerShell.

Управление обновлениями приложения осуществляется с помощью [параметров обновления](service-fabric-application-upgrade-parameters.md).

Узнайте, как использовать [сериализацию данных](service-fabric-application-upgrade-data-serialization.md), чтобы обеспечить совместимость обновлений приложения.

[Дополнительные разделы](service-fabric-application-upgrade-advanced.md) содержат сведения о работе с расширенными функциями при обновлении приложения.

Сведения об устранении распространенных проблем при обновлении приложений см. в разделе [Устранение неполадок обновления приложения](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0525_2016-->