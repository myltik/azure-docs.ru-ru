<properties
   pageTitle="Диспетчер кластерных ресурсов Service Fabric — политики размещения | Microsoft Azure"
   description="Общие сведения о дополнительных политиках размещения и правилах для служб Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Политики размещения для служб Service Fabric
Если ваш кластер Service Fabric охватывает широкую географическую область, например, включает несколько центров обработки данных или регионов Azure, либо ваша среда распространяется на несколько зон геополитического контроля (а также в некоторых других ситуациях, требующих соблюдения юридических или политических границ), приходится принимать во внимание множество дополнительных правил, а также учитывать удаленность, связанную с задержками и ухудшением производительности. Эти проблемы преимущественно можно решить, используя свойства узла и ограничения на размещение, но бывают и более сложные случаи. В любом случае, для каждой службы политики размещения (как и ограничения) можно настроить отдельно на уровне имени экземпляра службы.

## Указание недопустимых доменов
Политика размещения InvalidDomain позволяет указать, что для данной рабочей нагрузки определенный домен сбоя является недопустимым. Эта политика предотвращает выполнение определенной службы в определенном регионе, что может быть связано с геополитическими причинами или корпоративной политикой организации. Можно указать несколько недопустимых доменов.

![Пример недопустимого домена][Image1]

Код:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```
## Указание обязательных доменов
Политика размещения обязательных доменов требует, чтобы все реплики находились в указанном домене. Можно указать несколько обязательных доменов.

![Пример обязательного домена][Image2]

Код:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## Указание предпочтительного домена для первичных реплик
Предпочтительный домен для первичной реплики — интересный элемент управления, позволяющий выбрать домен сбоя, в котором должна находиться первичная реплика (если это возможно). Если все в порядке, первичная реплика оказывается в этом домене. Если по какой-либо причине домен или первичная реплика отказывают или отключаются, первичная реплика переносится в другое место. Как только появляется возможность, первичная реплика возвращается в предпочтительный домен. Обычно этот параметр имеет смысл только для служб с отслеживанием состояния. Эта политика особенно полезна в географически объединенных кластерах, где для обеспечения избыточности используются другие расположения, но вам хотелось бы, чтобы первичные реплики находились в определенном месте с целью сокращения задержки для операций, которые выполняются на первичной реплике (операции записи и по умолчанию все операции чтения обслуживаются первичной репликой).

![Предпочтительные домены для первичных реплик и отработка отказа][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

## Требовать распределения реплик между всеми доменами и запретить упаковку
Можно указать другую политику, согласно которой реплики всегда должны быть распределены между доступными доменами. Обратите внимание на то, что в приведенном выше примере несколько реплик могут временно оказаться упакованными в один и тот же центр обработки данных. В данном случае это, может быть, и не критично, поскольку первичная реплика не находится в том же центре обработки данных, что и большинство реплик, но в целом означает, что временно может сформироваться конфигурация, которой нам хотелось бы избежать. Это зависит от других существующих в кластере условий, однако в любом случае вы постараетесь не допустить подобного, распределив свои реплики между разными доменами. Следует помнить, что при этом вам придется сталкиваться с простоями и сбоями вместо предоставления службе разрешения возможности выполнения на оставшихся узлах. Такое поведение настраивает политика RequireDomainDistribution, однако она может означать, что в случае если размещение реплик приведет к упаковке, они не будут размещаться нигде. В результате на какое-то время, пока узлы не вернутся в жизнеспособный домен, число реплик может оказаться меньше заданного вами предела. Некоторые клиенты предпочитают иметь целевое количество реплик (копий состояния) в любой момент времени, другие не хотят рисковать доступностью — если реплики окажутся упакованы в домен сбоя и этот домен откажет, вы лишитесь всех этих реплик одновременно. Если реплик немного, это можно привести к потере кворума. В редком случае невозвращения домена это может привести к потере данных. Поскольку большинство людей работают больше чем с 3 репликами, по умолчанию распределение по доменам не требуется, а балансировка и отработка отказа выполняются в обычном режиме, даже если это означает, что в домен будут временно упакованы сразу несколько реплик.

Код:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Можно ли использовать эти конфигурации для служб в кластере, который не отличается широкой географией? Можно, но не нужно. В частности, не следует прибегать к обязательным, недопустимым и предпочтительным доменам, если ваш кластер не отличается широким географическим распределением.

## Дальнейшие действия
- Дополнительные сведения о других вариантах настройки служб см. в разделе о других конфигурациях диспетчера ресурсов кластера в статье [Дополнительные сведения о настройке служб](service-fabric-cluster-resource-manager-configure-services.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0525_2016-->