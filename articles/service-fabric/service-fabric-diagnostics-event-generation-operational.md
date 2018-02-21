---
title: "Операционный канал Azure Service Fabric | Документы Майкрософт"
description: "Полный список журналов, созданных в операционном канале кластеров Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 712679b8fae9059df602881f28a1b74f7244fca3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="operational-channel"></a>Операционный канал 

Операционный канал состоит из журналов действий высокого уровня, выполняемых Service Fabric на узлах и в кластере. При включении "Диагностики" для кластера агент системы диагностики Azure развертывается в кластере и по умолчанию настраивается для чтения журналов из операционного канала. Дополнительные сведения о настройке агента для изменения конфигурации диагностики кластера с целью выбора дополнительных журналов или счетчиков производительности см. в разделе [Агент системы диагностики Azure](service-fabric-diagnostics-event-aggregation-wad.md). 

## <a name="operational-channel-logs"></a>Журналы операционного канала 

Ниже приведен полный список журналов, созданных Service Fabric в операционном канале. 

| EventId | ИМЯ | Источник (задача) | Уровень |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Информация |
| 25621 | NodeOpenedSuccess | FabricNode | Информация |
| 25622 | NodeOpenedFailed | FabricNode | Информация |
| 25623 | NodeClosing | FabricNode | Информация |
| 25624 | NodeClosed | FabricNode | Информация |
| 25625 | NodeAborting | FabricNode | Информация |
| 25626 | NodeAborted | FabricNode | Информация |
| 29627 | ClusterUpgradeStart | CM | Информация |
| 29628 | ClusterUpgradeComplete | CM | Информация |
| 29629 | ClusterUpgradeRollback | CM | Информация |
| 29630 | ClusterUpgradeRollbackComplete | CM | Информация |
| 29631 | ClusterUpgradeDomainComplete | CM | Информация |
| 23074 | ContainerActivated | Hosting | Информация |
| 23075 | ContainerDeactivated | Hosting | Информация |
| 29620 | ApplicationCreated | CM | Информация |
| 29621 | ApplicationUpgradeStart | CM | Информация |
| 29622 | ApplicationUpgradeComplete | CM | Информация |
| 29623 | ApplicationUpgradeRollback | CM | Информация |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Информация |
| 29625 | ApplicationDeleted | CM | Информация |
| 29626 | ApplicationUpgradeDomainComplete | CM | Информация |
| 18566 | ServiceCreated | FM | Информация |
| 18567 | ServiceDeleted | FM | Информация |

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о создании событий на уровне платформы в Service Fabric см. в [этом разделе](service-fabric-diagnostics-event-generation-infra.md).
* Изменение конфигурации [системы диагностики Azure](service-fabric-diagnostics-event-aggregation-wad.md) для сбора дополнительных журналов
* [Настройка Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) для просмотра журналов операционного канала
