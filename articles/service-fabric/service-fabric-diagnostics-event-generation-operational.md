---
title: Список событий Azure Service Fabric | Документация Майкрософт
description: Полный список событий, предоставляемых Azure Service Fabric для мониторинга кластеров.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212965"
---
# <a name="list-of-service-fabric-events"></a>Список событий Service Fabric 

Service Fabric предоставляет основной набор событий кластера, чтобы сообщать о состоянии кластеров в виде [событий Service Fabric](service-fabric-diagnostics-events.md). Они основаны на действиях, выполненных Service Fabric на узлах и в кластерах, или решениях по управлению, принятых владельцем или оператором кластера. Эти события можно получить с помощью запроса к [EventStore](service-fabric-diagnostics-eventstore.md) в кластере или с помощью операционного канала. На компьютерах Windows операционный канал также подключен к журналу событий, поэтому события Service Fabric можно просматривать в средстве "Просмотр событий". 

>[!NOTE]
>Список событий Service Fabric для кластеров в версиях, предшествующих версии 6.2, приведен в следующем разделе. 

Ниже приведен список всех событий, доступных на платформе, отсортированный по сущности, к которой они относятся.

## <a name="cluster-events"></a>События кластера

**События обновления кластера**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Информация | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Информация | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Информация | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Информация | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Информация | 1 |

**События отчета о работоспособности кластера**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Информация | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Информация | 1 |

**События службы тестирования в условиях хаоса**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Тестирование | Информация | 1 |
| 50023 | ChaosStoppedEvent | Тестирование | Информация | 1 |

## <a name="node-events"></a>События узла

**События жизненного цикла узла** 

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Информация | 1 |
| 18603 | NodeUpOperational | FM | Информация | 1 |
| 18604 | NodeDownOperational | FM | Информация | 1 |
| 18605 | NodeAddedOperational | FM | Информация | 1 |
| 18606 | NodeRemovedOperational | FM | Информация | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Информация | 1 |
| 25620 | NodeOpening | FabricNode | Информация | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Информация | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Информация | 1 |
| 25623 | NodeClosing | FabricNode | Информация | 1 |
| 25624 | NodeClosed | FabricNode | Информация | 1 |
| 25625 | NodeAborting | FabricNode | Информация | 1 |
| 25626 | NodeAborted | FabricNode | Информация | 1 |

**События отчета о работоспособности узла**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Информация | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Информация | 1 |

**События узла в условиях хаоса**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Тестирование | Информация | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Тестирование | Информация | 1 |

## <a name="application-events"></a>События приложения

**События жизненного цикла приложения**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Информация | 1 |
| 29625 | ApplicationDeletedOperational | CM | Информация | 1 |
| 23083 | ProcessExitedOperational | Hosting | Информация | 1 |

**События обновления приложения**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Информация | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Информация | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Информация | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Информация | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Информация | 1 |

**События отчета о работоспособности приложения**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Информация | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Информация | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Информация | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Информация | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Информация | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Информация | 1 |

**События приложения в условиях хаоса**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Тестирование | Информация | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Тестирование | Информация | 1 |

## <a name="service-events"></a>События службы

**События жизненного цикла службы**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Информация | 1 |
| 18658 | ServiceDeletedOperational | FM | Информация | 1 |

**События отчета о работоспособности службы**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Информация | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Информация | 1 |

## <a name="partition-events"></a>События секции

**События перемещения секции**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | Информация | 1 |

**События отчета о работоспособности секции**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Информация | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Информация | 1 |

**События секции в условиях хаоса**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Тестирование | Информация | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Тестирование | Информация | 1 |

**События анализа секции**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Тестирование | Информация | 1 |

## <a name="replica-events"></a>События реплики

**События отчета о работоспособности реплики**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Информация | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Информация | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Информация | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Информация | 1 |

**События реплики в условиях хаоса**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Тестирование | Информация | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Тестирование | Информация | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Тестирование | Информация | 1 |

## <a name="container-events"></a>События контейнера

**События жизненного цикла контейнера** 

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hosting | Информация | 1 |
| 23075 | ContainerDeactivatedOperational | Hosting | Информация | 1 |
| 23082 | ContainerExitedOperational | Hosting | Информация | 1 |

## <a name="other-events"></a>Другие события

**События корреляции**

| EventId | ИМЯ | Источник (задача) | Уровень | Version (версия) |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Тестирование | Информация | 1 |

## <a name="events-prior-to-version-62"></a>События версии ниже 6.2

Ниже приведен полный список событий, предоставляемых Service Fabric до версии 6.2.

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
