---
title: "Регулирование в диспетчере кластерных ресурсов Service Fabric | Документация Майкрософт"
description: "Узнайте, как настраивать регулирование в диспетчере кластерных ресурсов Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Регулирование поведения диспетчера кластерных ресурсов Service Fabric
Даже если диспетчер кластерных ресурсов настроен правильно, работа кластера может быть нарушена. Например, отказы нескольких узлов или доменов сбоя могут произойти во время установки новой версии или при применении обновлений. Диспетчер ресурсов кластера попытается все исправить, но это может привести к оттоку в кластере. Регулирования обеспечивают поддержку, чтобы кластер мог самостоятельно стабилизировать работу (вернуть узлы в работоспособное состояние, восстановить работоспособность сети и развернуть исправленные биты).

Чтобы помочь в подобных ситуациях, диспетчер кластерных ресурсов Service Fabric включает в себя несколько регулировок. Это достаточно "большие пушки". Не стоит изменять значения по умолчанию этих параметров, пока вы тщательно не просчитали объем параллельных работ, которые можно выполнить в кластере.

Регулировки имеют значения по умолчанию, которые команда Service Fabric определила, исходя из собственного опыта. Если их необходимо изменить, настройте их в соответствии с ожидаемой фактической нагрузкой. Возможно, вы решите настроить несколько регулировок, даже если это означает, что в основных ситуациях стабилизация кластера будет занимать больше времени.

## <a name="configuring-the-throttles"></a>Настройка регулирования
По умолчанию включены следующие регулировки:

* GlobalMovementThrottleThreshold. Данный параметр управляет общим количеством перемещений в кластере за некоторое время (определяется в секундах как GlobalMovementThrottleCountingInterval)
* MovementPerPartitionThrottleThreshold. Данный параметр управляет общим количеством перемещений для любой секции службы за некоторое время (MovementPerPartitionThrottleCountingInterval, значение задается в секундах)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Для автономных развертываний используется ClusterConfig.json, а для размещенных в Azure кластеров — Template.json:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

Чаще всего клиенты используют эти регулировки при работе в среде с ограниченными ресурсами. Например, в некоторых средах будет ограничена пропускная способность сети для отдельных узлов, а в других диски не смогут создавать много реплик в параллельном режиме из-за ограничений пропускной способности. Такие ограничения означают, что операции, которые активируются в ответ на сбои, не будут выполняться или выполнятся, но медленно даже без регулирования. В таких случаях клиенты знали, что они потенциально увеличивают период стабилизации работы кластера, и что регулирование может привести к понижению общей надежности.

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы узнать, как диспетчер кластерных ресурсов управляет нагрузкой кластера и балансирует ее, ознакомьтесь со статьей о [балансировке нагрузки](service-fabric-cluster-resource-manager-balancing.md)
* Диспетчер кластерных ресурсов предоставляет много параметров для описания кластера. Чтобы узнать о них больше, ознакомьтесь со статьей [Описание кластера Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)



<!--HONumber=Jan17_HO1-->


