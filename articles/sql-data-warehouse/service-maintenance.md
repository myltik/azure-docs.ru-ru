---
title: Плановое обслуживание. Хранилище данных SQL Azure | Документация Майкрософт
description: Узнайте, как подготовиться к событиям планового обслуживания в хранилище данных SQL Azure.
services: sql-data-warehouse
author: antvgski
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a273ad4c256267865d3af324f0ef755a6cb75c5c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799895"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Планирование обслуживания для хранилища данных Azure SQL

Узнайте, как подготовиться к событиям планового обслуживания в хранилище данных SQL Azure.

## <a name="what-is-a-planned-maintenance-event"></a>Что такое событие планового обслуживания?
Событие планового обслуживания — это определенный период времени, на протяжении которого хранилище данных находится в автономном режиме для операций обслуживания. Такие события дают возможность внедрить новые возможности, применить обновления служб или исправления. 

## <a name="frequency"></a>Frequency
В среднем каждый месяц происходит по крайней мере одно событие планового обслуживания. 

## <a name="notifications-and-downtime"></a>Уведомления и время простоя
Перед каждым событием планового обслуживания вы получите уведомление. События планового обслуживания отменяют все выполняемые запросы и переводят хранилище данных в автономный режим. Для каждого хранилища данных прогнозируется время простоя около 30 минут. Вы также получите уведомление, когда обслуживание будет завершено. 

## <a name="setting-up-alerts"></a>Настройка оповещений

Мы рекомендуем настроить оповещения журнала планового обслуживания с помощью [Azure Monitor](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). Эти оповещения помогут вам подготовиться к обязательному обслуживанию и снизить его влияние на работу организации. 

Чтобы настроить уведомления, выполните эти [инструкции по созданию оповещений журналов](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о мониторинге см. в статье [Мониторинг рабочей нагрузки с помощью динамических административных представлений](sql-data-warehouse-manage-monitor.md).
