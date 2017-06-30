---
title: "Управление ресурсами Azure Service Fabric для контейнеров и служб | Документация Майкрософт"
description: "Azure Service Fabric позволяет указать ограничения ресурсов для служб, выполняемых внутри или вне контейнеров."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>управление ресурсами; 

При выполнении нескольких служб на одном узле или кластере одна служба может потреблять больше ресурсов, оставляя другие службы без них. Это также известно как проблема шумного соседа. Service Fabric позволяет разработчику указать резервы и ограничения для каждой службы, чтобы обеспечить их ресурсами, а также ограничить использование этих ресурсов. 

## <a name="resource-governance-metrics"></a>Метрики управления ресурсами 

Управление ресурсами поддерживается в Service Fabric для каждого [пакета службы](service-fabric-application-model.md). Ресурсы, которые назначены пакету службы, могут быть дополнительно разделены между пакетами кода. Заданные ограничения ресурсов также означают их резервирование. Service Fabric поддерживает указание ЦП и памяти для каждого пакета службы с помощью двух встроенных [метрик](service-fabric-cluster-resource-manager-metrics.md):

* ЦП (имя метрики — `ServiceFabric:/_CpuCores`): ядром является логическое ядро, которое доступно на хост-компьютере, и все ядра на всех узлах взвешиваются одинаково.
* Память (имя метрики — `ServiceFabric:/_MemoryInMB`) измеряется в мегабайтах и сопоставляется с физической памятью, доступной на компьютере.

Предоставляются только мягкие гарантии резервирования: среда выполнения отклоняет открытие новых пакетов служб при превышении доступных ресурсов. Однако если другой исполняемый файл или контейнер размещен на узле, это может нарушить изначальные гарантии резервирования.

Для обеих метрик [диспетчер ресурсов кластера](service-fabric-cluster-resource-manager-cluster-description.md) отслеживает общую емкость кластера, нагрузку в каждом узле кластера и оставшиеся в кластере ресурсы. Обе метрики эквивалентны для всех пользователей и аналогичны пользовательским метрикам. При этом с ними можно использовать все существующие функции:
* Кластер может быть [сбалансирован](service-fabric-cluster-resource-manager-balancing.md) в соответствии с этими двумя метриками (поведение по умолчанию).
* Кластер может быть [дефрагментирован](service-fabric-cluster-resource-manager-defragmentation-metrics.md) в соответствии с этими двумя метриками.
* При [описании кластера](service-fabric-cluster-resource-manager-cluster-description.md) для этих двух метрик можно задать емкость буфера.

[Передача данных о динамической нагрузке](service-fabric-cluster-resource-manager-metrics.md) не поддерживается этими метриками, и нагрузка для них определяется во время создания.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Настройка кластера для включения управления ресурсами

Емкость нужно определять вручную в каждом типе узла в кластере следующим образом:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
Управление ресурсами допускается только в пользовательских службах, но не в каких-либо системных службах. При указании емкости некоторые ядра и память должны оставаться нераспределенными для системных служб. Для достижения оптимальной производительности следующий параметр также должен быть включен в манифесте кластера: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>Указание управления ресурсами 

Ограничения ресурсов управления указываются в манифесте приложения (раздел ServiceManifestImport), как показано в следующем примере:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
В этом примере пакет службы ServicePackageA получает одно ядро на узлах, где он размещается. Этот пакет службы содержит два пакета кода (CodeA1 и CodeA2), и оба указывают параметр `CpuShares`. Пропорция CpuShares 512:256 разделяет ресурсы ядра между двумя пакетами кода. Таким образом в этом примере CodeA1 получает две трети ядра, а CodeA2 — одну треть (и соответствующие мягкие гарантии резервирования). Если параметр CpuShares не указан для пакетов кода, Service Fabric делит ядра между ними поровну.

Ограничения по памяти абсолютны, так что оба пакета кода ограничены 1024 МБ (и имеют соответствующие мягкие гарантии резервирования). Пакеты кода (контейнеры или процессы) не могут выделить больше памяти, чем задано ограничением, а при попытке сделать это возникнет проблема нехватки памяти. Чтобы принудительное ограничение ресурсов работало, для всех пакетов кода в пакете службы должны быть указаны ограничения по памяти.


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о диспетчере ресурсов кластера см. в [этой статье](service-fabric-cluster-resource-manager-introduction.md).
* Дополнительные сведения о модели приложения, пакетах служб, пакетах кода и способах сопоставления реплик с ними см. в [этой статье](service-fabric-application-model.md).

