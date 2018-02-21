---
title: "Мониторинг контейнеров в Azure Service Fabric с помощью OMS Log Analytics | Документация Майкрософт"
description: "Сведения о мониторинге контейнеров, работающих в кластерах Azure Service Fabric, с помощью OMS Log Analytics."
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
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 17121041520160d0d76832bbdbe74ad6a649fdd8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="monitor-containers-with-oms-log-analytics"></a>Мониторинг контейнеров с помощью OMS Log Analytics
 
В этой статье рассматриваются шаги, необходимые для настройки мониторинга контейнера в кластере. Дополнительные сведения см. в разделе [Отслеживание контейнеров](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Пошаговое руководство см. в статье [Мониторинг контейнера Windows в Service Fabric с помощью OMS](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Настройка решения мониторинга контейнера

> [!NOTE]
> Вам нужно настроить OMS Log Analytics для кластера и развернуть на узлах агент OMS. Если эти решения не установлены, сначала выполните действия, описанные в статьях [Настройка OMS Log Analytics для кластера](service-fabric-diagnostics-oms-setup.md) и [Add the OMS Agent to a cluster](service-fabric-diagnostics-oms-agent.md) (Добавление агента OMS в кластер).

1. После настройки кластера с помощью OMS Log Analytics и агента OMS разверните контейнеры. Прежде чем перейти к следующему шагу, подождите, пока ваши контейнеры развернутся.

2. Найдите *Решение мониторинга контейнеров* в Azure Marketplace и щелкните ресурс **Решения мониторинга контейнеров** в категории "Мониторинг и управление".

    ![Добавление решения "Контейнеры"](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Создайте решение внутри рабочей области, созданной для кластера. При таком изменении агент автоматически активируется для запуска сбора данных Docker в контейнеры. Приблизительно через 15 минут в решении появятся входящие журналы и статистика.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об оркестрации контейнера в Service Fabric см. в статье [Service Fabric и контейнеры](service-fabric-containers-overview.md)
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью решения Log Analytics.
* Настройте OMS, чтобы настроить правила [автоматических предупреждений](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.