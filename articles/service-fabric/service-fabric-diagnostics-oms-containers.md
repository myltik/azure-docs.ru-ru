---
title: Мониторинг контейнеров в Azure Service Fabric с помощью Log Analytics | Документация Майкрософт
description: Сведения о мониторинге контейнеров, работающих в кластерах Azure Service Fabric, с помощью Log Analytics.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Мониторинг контейнеров с помощью Log Analytics
 
В этой статье рассматриваются шаги, необходимые для настройки мониторинга контейнера в кластере. Дополнительные сведения см. в разделе [Отслеживание контейнеров](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Пошаговое руководство см. в статье [Мониторинг контейнера Windows в Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Настройка решения мониторинга контейнера

> [!NOTE]
> Вам нужно настроить Log Analytics для кластера и развернуть на узлах агент OMS. Если эти решения не установлены, сначала выполните действия, описанные в руководстве по [настройке Log Analytics для кластера](service-fabric-diagnostics-oms-setup.md) и [добавлению агента OMS в кластер](service-fabric-diagnostics-oms-agent.md).

1. После настройки кластера с помощью Log Analytics и агента OMS разверните контейнеры. Прежде чем перейти к следующему шагу, подождите, пока ваши контейнеры развернутся.

2. Найдите *Решение мониторинга контейнеров* в Azure Marketplace и щелкните ресурс **Решения мониторинга контейнеров** в категории "Мониторинг и управление".

    ![Добавление решения "Контейнеры"](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Создайте решение внутри рабочей области, созданной для кластера. При таком изменении агент автоматически активируется для запуска сбора данных Docker в контейнеры. Приблизительно через 15 минут в решении появятся входящие журналы и статистика.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об оркестрации контейнера в Service Fabric см. в статье [Service Fabric и контейнеры](service-fabric-containers-overview.md)
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью решения Log Analytics.
* Настройте в Log Analytics [правила автоматической генерации оповещений](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.