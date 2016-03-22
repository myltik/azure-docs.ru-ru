<properties
   pageTitle="Диспетчер кластерных ресурсов Service Fabric — политики размещения"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Политики размещения
Если ваш кластер Service Fabric охватывает широкую географическую область, например, включает несколько центров обработки данных или регионов Azure, либо ваша среда распространяется на несколько зон геополитического контроля (а также в некоторых других ситуациях, требующих соблюдения юридических или политических границ), приходится принимать во внимание множество дополнительных правил. Большинство из них можно настроить, используя свойства узла и ограничения на размещение (о которых мы говорили ранее), но бывают и более сложные случаи. Как бы то ни было, для каждой службы политики размещения (как и ограничения) можно настроить отдельно.

-	InvalidDomain: позволяет указать, что для данной рабочей нагрузки определенный домен сбоя является недопустимым. Эта политика предотвращает выполнение определенной службы в определенном регионе, что может быть связано с геополитическими причинами или корпоративной политикой организации.

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

-	RequiredDomain: требует, чтобы все реплики находились в указанном домене.

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

-	PreferPrimaryDomain: предпочтительный домен для первичной реплики — интересный элемент управления, позволяющий выбрать домен сбоя, в котором должна находиться первичная реплика (если это возможно). Если все в порядке, первичная реплика оказывается в этом домене. Если по какой-либо причине домен или первичная реплика отказывают или отключаются, первичная реплика переносится в другое место. Как только появляется возможность, первичная реплика возвращается в предпочтительный домен. Обычно этот параметр имеет смысл только для служб с отслеживанием состояния.

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

-	RequireDomainDistribution: требование о распределении по доменам — еще один способ предотвращения определенных ситуаций. Обратите внимание на то, что в приведенном выше примере несколько реплик могут временно оказаться упакованными в один и тот же центр обработки данных. В данном случае это, может быть, и не страшно, поскольку первичная реплика не находится в том же центре обработки данных, что и большинство реплик, но в целом означает, что временно может сформироваться конфигурация, которой нам хотелось бы избежать. Это зависит от других существующих в кластере условий, однако в любом случае вы постараетесь не допустить подобного, распределив свои реплики между разными доменами. Флаг RequireDomainDistribution предоставляет такую возможность, но может означать, что в случае если размещение реплик приведет к упаковке, они не будут размещаться нигде. В результате на какое-то время, пока узлы не вернутся в жизнеспособный домен, число реплик может оказаться меньше заданного вами предела. Некоторые клиенты предпочитают иметь целевое количество реплик (копий состояния) в любой момент времени, другие не хотят рисковать доступностью — если реплики окажутся упакованы в домен сбоя и этот домен откажет, вы лишитесь всех этих реплик одновременно. Если реплик немного, это можно привести к потере кворума. Поскольку большинство людей работают больше чем с 3 репликами, по умолчанию распределение по доменам не требуется, а балансировка и отработка отказа выполняются в обычном режиме, даже если это означает, что в домен будет временно упакованы сразу несколько реплик.

Код:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Для одной службы можно настроить несколько недопустимых или обязательных доменов, а остальные параметры (предпочтительный домен и требование распределения по доменам) могут иметь только по одному значению.

Можно ли использовать эти конфигурации для служб в кластере, который не отличается широкой географией? Можно, но не нужно. В частности, не следует прибегать к обязательным, недопустимым и предпочтительным доменам, если ваш кластер не отличается широким географическим распределением.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия
- [Дополнительные сведения о настройке служб](service-fabric-cluster-resource-manager-configure-services.md)



[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0309_2016-->