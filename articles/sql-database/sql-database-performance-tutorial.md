---
title: "Устранение проблем с производительностью и оптимизация базы данных | Документация Майкрософт"
description: "Применяйте рекомендации по повышению производительности базы данных SQL, а также узнайте, как получить ценные сведения о производительности запросов базы данных"
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: performance
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0bdb111257053e4a57510a72ab683bc9c890d2f7
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>Устранение проблем с производительностью и оптимизация базы данных

Отсутствующие индексы и плохо оптимизированные запросы — распространенные причины низкой производительности баз данных. Из этого руководства вы узнаете, как выполнять такие задачи.
> [!div class="checklist"]
> * Просмотр и применение рекомендаций по улучшению производительности и их отмена.
> * Поиск ресурсоемких запросов.
> * Поиск долго выполняющихся запросов.

> Чтобы получить рекомендации, вам нужна непрерывная рабочая нагрузка в базе данных с проблемами с производительностью, такими как отсутствующий индекс.
>

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="review-and-apply-a-recommendation"></a>Просмотр и применение рекомендации

Чтобы применить рекомендацию из системы для базы данных, сделайте следующее:

1. В колонке базы данных щелкните меню **Рекомендации по производительности**.

    ![рекомендации по производительности](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. Выберите активную рекомендацию из списка рекомендаций. В нашем примере это "Создание индекса".

    ![выбор рекомендации](./media/sql-database-performance-tutorial/create_index.png)

3. Примените рекомендацию, нажав кнопку **Применить**. При необходимости просмотрите сведения о рекомендации и выполняемый скрипт T-SQL, нажав кнопку **Показать скрипт**.

    ![применение рекомендации](./media/sql-database-performance-tutorial/apply.png)

4. [Необязательно.] Включите автоматическую настройку для автоматического применения рекомендаций.

    ![автоматическая настройка](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>Отмена рекомендации

Помощник по работе с базами данных отслеживает каждую внедренную рекомендацию. Если после внедрения рекомендации производительность рабочей нагрузки не увеличилась, она будет автоматически отменена. Рекомендацию также можно отменить вручную, но в большинстве случаев это не требуется. Отмена рекомендации

1. Перейдите в меню рекомендаций по производительности и выберите одну из примененных рекомендаций.

    ![выбор рекомендации](./media/sql-database-performance-tutorial/select.png)

2. В представлении сведений щелкните **Отменить изменения**.

    ![отмена рекомендации](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>Поиск самого ресурсоемкого запроса

Чтобы найти запрос, использующий больше всего ресурсов, сделайте следующее:

1. В колонке базы данных щелкните меню **Анализ производительности запросов**.

    ![анализ запросов](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. Выберите тип ресурсов.

    ![анализ запросов](./media/sql-database-performance-tutorial/select_resource_type.png)

3. Выберите первый запрос в таблице.

    ![анализ запросов](./media/sql-database-performance-tutorial/select_query.png)

4. Просмотрите сведения о запросе.

    ![анализ запросов](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>Поиск самого длительного запроса

1. Перейдите в меню "Анализ производительности запросов" и выберите вкладку **Длительные запросы**.

    ![анализ запросов](./media/sql-database-performance-tutorial/long_running.png)

3. Выберите первый запрос в таблице.

    ![анализ запросов](./media/sql-database-performance-tutorial/select_first_query.png)

4. Просмотрите сведения о запросе.

    ![анализ запросов](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>Дальнейшие действия 
Отсутствующие индексы и плохо оптимизированные запросы — распространенные причины низкой производительности баз данных. В этом учебнике вы научились выполнять следующие задачи:
> [!div class="checklist"]
> * Просмотр и применение рекомендаций по улучшению производительности и их отмена.
> * Поиск ресурсоемких запросов.
> * Поиск долго выполняющихся запросов.

[Советы по настройке производительности базы данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

