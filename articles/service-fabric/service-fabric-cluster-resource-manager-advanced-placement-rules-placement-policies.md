---
title: "Диспетчер кластерных ресурсов Service Fabric — политики размещения | Документация Майкрософт"
description: "Общие сведения о дополнительных политиках размещения и правилах для служб Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>Политики размещения для служб Service Fabric
Существует много разных дополнительных правил, которые требуется настраивать в некоторых редких случаях. Вот несколько примеров.
* Кластер Service Fabric распределен между несколькими географическими регионами (включая регионы Azure) или локальными центрами обработки данных.
* Среда охватывает несколько геополитических зон (или в другой ситуации, когда нужно учитывать наличие юридических или политических границ).
* Есть требования к фактическим показателям производительности и задержки, связанным с передачей данных в кластере на большие расстояния или через определенные медленные или ненадежные сети.

В таких случаях службу бывает полезно настроить, определив для нее выполнение при любых условиях или только в определенных регионах. Кроме того, вы можете попробовать разместить первичную реплику в определенном регионе, чтобы минимизировать задержки для пользователей.

Дополнительные политики размещения используются в следующих ситуациях:

1. с недопустимыми доменами;
2. с обязательными доменами;
3. с предпочтительными доменами;
4. при запрете упаковки реплик.

Большинство перечисленных ниже элементов управления можно настроить, используя свойства узла и ограничения на размещение, но бывают и более сложные случаи. Чтобы упростить процесс, диспетчер кластерных ресурсов Service Fabric предоставляет дополнительные политики размещения. Политики размещения (как и другие ограничения размещения) можно настроить отдельно на уровне именованного экземпляра службы и динамически обновлять.

## <a name="specifying-invalid-domains"></a>Указание недопустимых доменов
Политика размещения InvalidDomain позволяет указать, что для данной рабочей нагрузки определенный домен сбоя является недопустимым. Эта политика предотвращает выполнение определенной службы в определенном регионе, что может быть связано с геополитическими причинами или корпоративной политикой организации. Используя отдельные политики, можно указать несколько недопустимых доменов.

<center>
![Пример недопустимого домена][Image1]
</center>

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
## <a name="specifying-required-domains"></a>Указание обязательных доменов
Политика размещения обязательных доменов предполагает, что все реплики с отслеживанием состояния или экземпляры службы без отслеживания состояния должны находиться в указанном домене. Используя отдельные политики, можно указать несколько обязательных доменов.

<center>
![Пример обязательного домена][Image2]
</center>

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

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Указание предпочтительного домена для первичных реплик
Предпочтительный домен для первичной реплики — интересный элемент управления, позволяющий выбрать домен сбоя, в котором должна находиться первичная реплика (если это возможно). Если все компоненты работоспособны, первичная реплика оказывается в этом домене. Если по какой-либо причине домен или первичная реплика завершают работу с ошибкой или отключаются, первичная реплика переносится в другое расположение. Если это расположение находится не в предпочтительном домене, диспетчер кластерных ресурсов при первой возможности возвращает реплику в предпочтительный домен. Обычно этот параметр имеет смысл только для служб с отслеживанием состояния. Эту политику особенно полезно использовать с кластерами, которые распределены между регионами Azure или несколькими центрами обработки данных, если первичные реплики должны находиться в определенном расположении. Когда первичные реплики расположены близко к пользователям, это обеспечивает минимум задержек, особенно при операциях чтения.

<center>
![Предпочтительные домены для первичных реплик и отработка отказа][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Обязательное распределение реплик между всеми доменами и запрет упаковки
Реплики _обычно_ распределяются между доменами работоспособного кластера. Но бывают случаи, когда реплики для указанного раздела могут быть временно упакованы в один домен. Допустим, в кластере есть девять узлов в трех доменах сбоя (fd:/0, fd:/1 и fd:/2), а у вашей службы есть три реплики. Предположим, что узлы, которые использовались для этих реплик в доменах fd:/1 и fd:/2, вышли из строя. В такой ситуации диспетчер кластерных ресурсов, как правило, использует другие узлы в этих же доменах сбоя. В нашем же примере, скажем, из-за проблем с емкостью все узлы в этих доменах являются недопустимыми. Если диспетчер кластерных ресурсов попытается заменить эти реплики, будут выбраны узлы в домене fd:/0. Но _тогда_ возникает ситуация, когда нарушается ограничение домена сбоя. А также возрастает вероятность того, что весь набор реплик выйдет из строя или будет утерян (если домен сбоя 0 будет навсегда утерян). Дополнительные сведения об общих ограничениях и приоритетах ограничений см. в [этом разделе](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).

Если вы увидели предупреждение о состоянии работоспособности наподобие такого: `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`, это значит, что у вас возникла описанная ошибка или сходная с ней. Такие ситуации возникают редко, но они возможны. Как правило, такие ошибки являются временными, так как узлы возвращаются в работоспособное состояние. Если узлы остаются в неработоспособном состоянии, а диспетчеру кластерных ресурсов требуется выполнить замену, для этого в идеальной ситуации в кластерах сбоя доступны другие узлы.

Для некоторых рабочих нагрузок лучше всегда иметь целевое число реплик, даже если они упакованы в меньшее число доменов. Такие рабочие нагрузки обеспечивают защиту от общего числа постоянных одновременных сбоев в домене, позволяя восстановить локальное состояние. А для других рабочих нагрузок предпочтительнее будет перейти в состояние простоя, чем подвергнуться риску потери данных или нарушения их целостности. Так как большинство производственных рабочих нагрузок работает больше чем с тремя репликами, больше чем с тремя доменами сбоя и множеством допустимых узлов в каждом домене сбоя, по умолчанию распределение в доменах не требуется. Это обеспечивает обычную ситуативную балансировку нагрузки и отработку отказа, даже если это означает, что в домен временно будет упаковано несколько реплик.

Если вы хотите отключить такой способ упаковки для определенной нагрузки, в службе можно указать политику RequireDomainDistribution. Если задать эту политику, диспетчер кластерных ресурсов запретит использовать две реплики из одного и того же раздела в одном домене сбоя или обновления.

Код:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Можно ли использовать эти конфигурации для служб в кластере, который не распределен между разными географическими регионами? Можно, но не нужно. Не следует прибегать к обязательным, недопустимым и предпочтительным доменам, если ваш кластер не распределен между несколькими регионами. Не имеет смысла пытаться принудительно запускать определенную рабочую нагрузку в одной стойке или использовать какой-либо сегмент локального кластера вместо другого. Разные аппаратные конфигурации следует распределить между доменами, включая те, которые обрабатываются с использованием обычных ограничений на размещение и свойств узлов.

## <a name="next-steps"></a>Дальнейшие действия
* См. дополнительные сведения о других [параметрах, доступных для настройки служб](service-fabric-cluster-resource-manager-configure-services.md).

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


