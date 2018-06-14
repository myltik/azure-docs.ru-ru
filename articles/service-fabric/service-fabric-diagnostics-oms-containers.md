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
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 79d30a47b017379107b63b0006a35534f68c43b9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210782"
---
# <a name="monitor-containers-with-log-analytics"></a>Мониторинг контейнеров с помощью Log Analytics
 
В этой статье описано, как настроить решение мониторинга контейнера OMS Log Analytics для просмотра событий контейнера. См. дополнительные сведения о [настройке кластера для сбора событий контейнера](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Настройка решения мониторинга контейнера

> [!NOTE]
> Вам нужно настроить Log Analytics для кластера и развернуть на узлах агент OMS. Если эти решения не установлены, сначала выполните действия, описанные в руководстве по [настройке Log Analytics для кластера](service-fabric-diagnostics-oms-setup.md) и [добавлению агента OMS в кластер](service-fabric-diagnostics-oms-agent.md).

1. После настройки кластера с помощью Log Analytics и агента OMS разверните контейнеры. Прежде чем перейти к следующему шагу, подождите, пока ваши контейнеры развернутся.

2. Найдите *Решение мониторинга контейнеров* в Azure Marketplace и щелкните ресурс **Решения мониторинга контейнеров** в категории "Мониторинг и управление".

    ![Добавление решения "Контейнеры"](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Создайте решение внутри рабочей области, созданной для кластера. При таком изменении агент автоматически активируется для запуска сбора данных Docker в контейнеры. Приблизительно через 15 минут в решении появятся входящие журналы и статистика, как показано на рисунке ниже.

    ![Основная панель мониторинга OMS](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Агент обеспечивает сбор нескольких журналов, относящихся к контейнерам, которые могут запрашиваться в OMS или использоваться для визуализации показателей эффективности. Собираются следующие типы журналов.

* ContainerInventory — отображает информацию о местонахождении контейнера, имени и образах.
* ContainerImageInventory — информация о развернутых образах, в том числе их идентификаторы или размеры.
* ContainerLog — журналы конкретных ошибок, журналы Docker (StdOut и т. д.) и другие записи.
* ContainerServiceLog — выполнявшиеся команды управляющей программы Docker.
* Perf — счетчики производительности контейнера, в том числе ЦП, память, сетевой трафик, операции ввода и вывода на диске и пользовательские метрики с компьютеров узла.



## <a name="next-steps"></a>Дополнительная информация
* См. дополнительные сведения о ](../log-analytics/log-analytics-containers.md)решении OMS "Контейнеры"[.
* Дополнительные сведения об оркестрации контейнера в Service Fabric см. в статье [Service Fabric и контейнеры](service-fabric-containers-overview.md)
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью решения Log Analytics.
* Настройте в Log Analytics [правила автоматической генерации оповещений](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.