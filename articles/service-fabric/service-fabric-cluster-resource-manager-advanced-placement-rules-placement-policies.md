---
title: Диспетчер кластерных ресурсов Service Fabric — политики размещения | Microsoft Docs
description: Общие сведения о дополнительных политиках размещения и правилах для служб Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Политики размещения для служб Service Fabric
Если ваш кластер Service Fabric охватывает широкую географическую область, например, включает несколько центров обработки данных или регионов Azure, либо ваша среда распространяется на несколько зон геополитического контроля (а также в некоторых других ситуациях, требующих соблюдения юридических или политических границ), приходится принимать во внимание множество дополнительных правил, а также учитывать удаленность, связанную с задержками и ухудшением производительности. Эти проблемы преимущественно можно решить, используя свойства узла и ограничения на размещение, но бывают и более сложные случаи. Для упрощения процесса доступны дополнительные команды. Для каждой службы политики размещения (как и ограничения) можно настроить отдельно на уровне имени экземпляра службы.

## Указание недопустимых доменов
Политика размещения InvalidDomain позволяет указать, что для данной рабочей нагрузки определенный домен сбоя является недопустимым. Эта политика предотвращает выполнение определенной службы в определенном регионе, что может быть связано с геополитическими причинами или корпоративной политикой организации. Используя отдельные политики, можно указать несколько недопустимых доменов.

![Пример недопустимого домена][Image1]

Код:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## Указание обязательных доменов
Политика размещения обязательных доменов требует, чтобы все реплики с отслеживанием состояния или экземпляры службы без отслеживания состояния находились в указанном домене. Используя отдельные политики, можно указать несколько обязательных доменов.

![Пример обязательного домена][Image2]

Код:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## Указание предпочтительного домена для первичных реплик
Предпочтительный домен для первичной реплики — интересный элемент управления, позволяющий выбрать домен сбоя, в котором должна находиться первичная реплика (если это возможно). Если все в порядке, первичная реплика оказывается в этом домене. Если по какой-либо причине домен или первичная реплика отказывают или отключаются, первичная реплика переносится в другое место. Если это место не располагается в предпочтительном домене, то диспетчер кластерных ресурсов (когда это возможно) возвращает ее в предпочтительный домен. Обычно этот параметр имеет смысл только для служб с отслеживанием состояния. Эта политика наиболее полезна для кластеров, которые распределены по регионам Azure или нескольким центрам обработки данных. В этих ситуациях для обеспечения избыточности вы используете все расположения, но вам хотелось бы, чтобы первичные реплики находились в определенном месте с целью сокращения задержки для операций, которые выполняются на первичной реплике (операции записи и по умолчанию все операции чтения обслуживаются первичной репликой).

![Предпочтительные домены для первичных реплик и отработка отказа][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## Обязательное распределение реплик между всеми доменами и запрет упаковки
Можно указать другую политику, согласно которой реплики всегда должны быть распределены между доступными доменами сбоя. Это происходит по умолчанию в большинстве случаев, когда кластер работоспособен, однако бывают случаи вырождения, когда реплики для определенного раздела оказываются временно упакованными в один домен сбоя или домен обновления. Предположим, в кластере есть 9 узлов в 3 доменах сбоя (0, 1 и 2), а ваша служба имеет 3 реплики. Узлы, которые использовались для этих реплик в доменах сбоя 1 и 2, вышли из строя, однако из-за проблем с емкостью ни один из оставшихся узлов в этих доменах не являлся допустимым. Если службе Service Fabric требуется создать замены для этих реплик, то диспетчер кластерных ресурсов должен поместить их в домен сбоя 0, но при этом возникает ситуация, когда нарушается ограничение на размещение в домене сбоя. А также возрастает вероятность того, что весь набор реплик будет потерян (если будет навсегда потерян домен сбоя 0). (Дополнительные сведения об ограничениях и о приоритетах ограничений в целом см. в [этом разделе](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)).

Если отображается предупреждение о работоспособности, подобное этому, значит сложилась именно такая ситуация или похожая на нее: "Load Balancer обнаружил нарушение ограничения для этой реплики: fabric:/<имя\_службы> Secondary Partition <идентификатор\_раздела> is violating the Constraint: FaultDomain". Обычно эти ситуации являются временными (узлы не выходят из строя на длительный срок, а если это и происходит и требуется создание замен, то в доменах сбоя есть другие узлы, которые являются допустимыми), но существуют такие рабочие нагрузки, которые могут пожертвовать доступностью, когда есть риск потери всех реплик. Указав политику RequireDomainDistribution, мы гарантируем, что в одном домене сбоя или домене обновления никогда не будут размещены две реплики из одного и того же раздела.

Для некоторых рабочих нагрузок рекомендуется всегда иметь целевое число реплик (копий состояния), гарантируя при этом защиту от общего числа сбоев в домене и восстановление локального состояния реплик. Для других рабочих нагрузок предпочтительней перейти в состояние простоя, чем подвергнуться риску потери данных или нарушения их целостности. Поскольку большинство производственных рабочих нагрузок работает больше чем с 3 репликами, по умолчанию распределение по доменам не требуется, а балансировка и отработка отказа выполняются в обычном режиме, даже если это означает, что в домен будут временно упакованы сразу несколько реплик.

Код:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Можно ли использовать эти конфигурации для служб в кластере, который не отличается широкой географией? Можно, но не нужно. В частности, не следует прибегать к обязательным, недопустимым и предпочтительным доменам, если ваш кластер не отличается широким географическим распределением. Не имеет смысла добиваться того, чтобы определенная рабочая нагрузка принудительно выполнялась в одной стойке, или чтобы какой-то сегмент локального кластера использовался вместо другого, если только не применяются различные типы оборудования или не происходит сегментация рабочей нагрузки. Для таких случаев можно применить обычные ограничения на размещение.

## Дальнейшие действия
* Дополнительные сведения о других вариантах настройки служб см. в статье [Настройка параметров диспетчера кластерных ресурсов для служб Service Fabric](service-fabric-cluster-resource-manager-configure-services.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0824_2016-->