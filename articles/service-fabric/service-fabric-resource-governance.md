---
title: Управление ресурсами Azure Service Fabric для контейнеров и служб | Документация Майкрософт
description: Azure Service Fabric позволяет указать ограничения ресурсов для служб, выполняемых внутри или вне контейнеров.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 49c7e2c99cce13880781a67806543b1cde0c12b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208018"
---
# <a name="resource-governance"></a>управление ресурсами; 

При выполнении нескольких служб на одном узле или кластере одна служба может потреблять больше ресурсов, оставляя другие работающие службы без них. Это также известно как проблема "шумного соседа". Azure Service Fabric позволяет разработчику настроить резервирование и ограничения для каждой службы, чтобы обеспечить их ресурсами, а также ограничить использование этих ресурсов.

> Прежде чем продолжить работу с этой статьей, рекомендуется ознакомиться с [моделью приложения Service Fabric](service-fabric-application-model.md) и [моделью размещения Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Метрики управления ресурсами 

Управление ресурсами поддерживается в Service Fabric в соответствии с каждым [пакетом службы](service-fabric-application-model.md). Ресурсы, которые назначены пакету службы, могут быть дополнительно разделены между пакетами кода. Заданные ограничения ресурсов также означают их резервирование. Service Fabric поддерживает указание ресурсов ЦП и памяти для каждого пакета службы с помощью двух встроенных [метрик](service-fabric-cluster-resource-manager-metrics.md):

* *ЦП* (имя метрики — `servicefabric:/_CpuCores`): логические ядра, доступные на хост-компьютере. Веса всех ядер для всех узлов считаются одинаковыми.

* *Память* (имя метрики — `servicefabric:/_MemoryInMB`): измеряется в мегабайтах и сопоставляется с физической памятью, доступной на компьютере.

Для обеих метрик [диспетчер кластерных ресурсов](service-fabric-cluster-resource-manager-cluster-description.md) отслеживает общую емкость кластера, нагрузку на каждом узле кластера и оставшиеся в кластере ресурсы. Эти две метрики аналогичны любой другой пользовательской или настраиваемой метрике. Их можно использовать для всех существующих функций:
* Кластер может быть [сбалансирован](service-fabric-cluster-resource-manager-balancing.md) в соответствии с этими двумя метриками (реакция на событие по умолчанию).
* Кластер может быть [дефрагментирован](service-fabric-cluster-resource-manager-defragmentation-metrics.md) в соответствии с этими двумя метриками.
* При [описании кластера](service-fabric-cluster-resource-manager-cluster-description.md) для этих двух метрик можно задать емкость буфера.

[Передача данных о динамической нагрузке](service-fabric-cluster-resource-manager-metrics.md) не поддерживается этими метриками, и нагрузка для них определяется во время создания.

## <a name="resource-governance-mechanism"></a>Механизм управления ресурсами

Среда выполнения Service Fabric в настоящее время не поддерживает резервирование ресурсов. Если открыт процесс или контейнер, среда выполнения задаст ограничение — ресурсы будут ограничены нагрузками, определенными в момент создания. Более того, среда выполнения будет отклонять открытие новых пакетов служб, которые доступны при превышении ограничения ресурсов. Чтобы лучше понять сам процесс, рассмотрим пример — узел с 2 ядрами ЦП (механизм управления памятью аналогичен).

1. Сначала на узле размещается контейнер, которому требуется 1 ядро ЦП. Среда выполнения открывает этот контейнер и задает ограничение в 1 ядро ЦП. Этот контейнер не сможет использовать более 1 ядра.

2. Затем на узле размещается реплика службы и с помощью соответствующего пакета службы задается ограничение в 1 ядро ЦП. Среда выполнения открывает пакет кода и задает для него ограничение в 1 ядро ЦП.

На этом этапе сумма ограничений равна емкости узла. Процесс и контейнер используют по одному ядру и не конфликтуют друг с другом. Service Fabric не размещает большее количество контейнеров или реплик, если для них задано ограничение ЦП.

Однако существуют две ситуации, в которых другие процессы могут состязаться за ЦП. В этих ситуациях у процесса и контейнера из нашего примера может возникнуть проблема "шумного соседа".

* *Сочетание управляемых и не управляемых служб и контейнеров*: если пользователь создает службу и не указывает управление ресурсами, среда выполнения решит, что она не использует ресурсы, и разместит ее на узле из нашего примера. В таком случае этот новый процесс будет активно использовать некоторые ресурсы ЦП за счет служб, которые уже запущены на узле. Эту проблему можно устранить двумя способами. Для этого не следует смешивать управляемые и не управляемые службы в одном кластере или следует использовать [ограничения размещения](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md), чтобы службы этих двух типов использовали разные наборы узлов.

* *Когда на узле запущен другой процесс, который не относится к процессам Service Fabric (например, служба ОС)*: в этой ситуации процесс извне Service Fabric также будет состязаться за ресурсы ЦП с имеющимися службами. Избежать этой проблемы можно, правильно настроив емкость узла с учетом дополнительной нагрузки на ОС, как описано в разделе ниже.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Настройка кластера для включения управления ресурсами

Когда узел запустится и присоединится к кластеру, Service Fabric обнаружит доступный объем памяти и доступное количество ядер и задаст емкости узла для этих двух ресурсов. 

Чтобы оставить место в буфере для операционной системы и других процессов, которые могут быть запущены на узле, Service Fabric будет использовать только 80 % от доступных на нем ресурсов. Это процентное значение можно настроить и изменить в манифесте кластера. 

Ниже приведен пример, как настроить Service Fabric для использования 50 % от доступных ресурсов ЦП и 70 % от доступной памяти: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

В случае, если емкости узлов нужно настроить только вручную, можно также использовать стандартный механизм описания узлов в кластере. Ниже приведен пример настройки узла с 4 ядрами и 2 ГБ памяти. 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Если включено автоматическое обнаружение доступных ресурсов и в манифесте кластера емкости узлов определены вручную, Service Fabric проверит, достаточно ли в узле ресурсов для поддержки определенной пользователем емкости.
* Если емкостей узлов, определенных в манифесте, недостаточно для доступных на узле ресурсов (или же хватает), Service Fabric будет использовать емкости, указанные в манифесте.

* Если емкости узлов, определенные в манифесте, превышают количество доступных ресурсов, Service Fabric будет использовать эти доступные ресурсы как емкости узла.

Если автоматическое обнаружение доступных ресурсов не требуется, его можно отключить. Для этого измените приведенный ниже параметр.

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Для достижения оптимальной производительности следующий параметр также должен быть включен в манифесте кластера: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Настройка управления ресурсами 

Ограничения ресурсов управления указываются в манифесте приложения (раздел ServiceManifestImport), как показано в следующем примере:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
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
  
В этом примере пакет службы **ServicePackageA** получает одно ядро на узлах, где он размещается. Этот пакет службы содержит два пакета кода (**CodeA1** и **CodeA2**), и оба указывают параметр `CpuShares`. Пропорция CpuShares 512:256 разделяет ресурсы ядра между двумя пакетами кода. 

Таким образом в этом примере CodeA1 получает две трети ядра, а CodeA2 — одну треть (и соответствующие мягкие гарантии резервирования). Если параметр CpuShares не указан для пакетов кода, Service Fabric делит ядра между ними поровну.

Ограничения по памяти абсолютны, так что оба пакета кода ограничены 1024 МБ (и имеют соответствующие мягкие гарантии резервирования). Пакеты кода (контейнеры или процессы) не могут выделить больше памяти, чем задано ограничением, а при попытке сделать это возникнет исключение из-за нехватки памяти. Чтобы принудительное ограничение ресурсов работало, для всех пакетов кода в пакете службы должны быть указаны ограничения по памяти.

### <a name="using-application-parameters"></a>Использование параметров приложения

При указании системы управления ресурсами можно использовать [параметры приложения](service-fabric-manage-multiple-environment-app-configuration.md), чтобы управлять несколькими конфигурациями приложений. Ниже приведен пример с использованием параметров приложения.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

В этом примере задаются значения параметров по умолчанию для рабочей среды, в которой каждому пакету службы выделяются 4 ядра и 2 ГБ памяти. Значения по умолчанию можно изменить с помощью файлов параметров приложения. В этом примере один файл параметров может использоваться для тестирования приложения в локальной среде, в которой ему предоставляется меньше ресурсов, чем в рабочей среде. 

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT] 
> Указание системы управления ресурсами с помощью параметров приложения доступно, начиная с Service Fabric версии 6.1.<br> 
>
> Если параметры приложения используются для указания системы управления ресурсами, то невозможно перейти на использование версии Service Fabric, более ранней, чем версия 6.1. 


## <a name="other-resources-for-containers"></a>Другие ресурсы для контейнеров
Помимо ограничений ресурсов ЦП и памяти можно указать другие ограничения ресурсов для контейнеров. Эти ограничения указываются на уровне пакета кода и будут применены при запуске контейнера. В отличие от ограничений ресурсов ЦП и памяти, диспетчер кластерных ресурсов не будет учитывать эти ресурсы и не будет выполнять проверки емкости или балансировку нагрузки для них. 

* *MemorySwapInMB* — объем памяти подкачки, которую может использовать контейнер.
* *MemoryReservationInMB* — мягкое ограничение по управлению памятью, которое применяется, только когда на узле обнаруживается состязание за память.
* *CpuPercent* — процент ресурсов ЦП, который может использовать контейнер. Если ограничения ресурсов ЦП указаны для пакета службы, этот параметр игнорируется.
* *MaximumIOps* — максимальное количество операций ввода-вывода, которое может использовать контейнер (для чтения и записи).
* *MaximumIOBytesps* — максимальное количество операций ввода-вывода (байт в секунду), которое может использовать контейнер (для чтения и записи).
* *BlockIOWeight* — число операций ввода-вывода в блоке по отношению к другим контейнерам.

Эти ресурсы можно объединять с ресурсами ЦП и памяти. Ниже приведен пример определения дополнительных ресурсов для контейнеров:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о диспетчере кластерных ресурсов см. в статье [Общие сведения о диспетчере кластерных ресурсов Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Дополнительные сведения о модели приложения, пакетах служб и пакетах кода см. в статье [Моделирование приложения в структуре службы](service-fabric-application-model.md).
