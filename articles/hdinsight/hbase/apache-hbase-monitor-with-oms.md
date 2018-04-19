---
title: Мониторинг HBase с помощью Log Analytics в Azure HDInsight | Документация Майкрософт
description: Использование Azure Log Analytics для мониторинга кластеров HDInsight HBase.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 3746713cdadff0a4c6f4fe25d278e8d78555f9d6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-hbase-with-log-analytics"></a>Мониторинг HBase с помощью Log Analytics

Решение мониторинга HDInsight HBase использует Azure Log Analytics для сбора метрик производительности HDInsight HBase из узлов кластера HDInsight. Монитор предоставляет визуализации и панели мониторинга для кластеров HBase, средства для поиска метрик и возможность создавать пользовательские правила мониторинга и оповещения. Вы можете отслеживать метрики для нескольких кластеров HDInsight HBase в нескольких подписках Azure.

Log Analytics — это служба в [Azure](../../operations-management-suite/operations-management-suite-overview.md), которая отслеживает облачные и локальные среды, чтобы поддерживать уровень их доступности и производительности. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников.

[Решения по управлению Log Analytics](../../log-analytics/log-analytics-add-solutions.md) добавляют функциональные возможности службе Log Analytics, тем самым предоставляя дополнительные средства анализа и работы с данными. Решения для управления Log Analytics представляют собой коллекцию логики, визуализации и правил получения данных, предоставляющую метрики, связанные с определенной областью. Решение также может определять новые типы записей для сбора, которые можно проанализировать с помощью функции поиска по журналам или новых возможностей пользовательского интерфейса.

В основе [Insight and Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) лежит платформа Log Analytics. Вы можете использовать стандартные возможности Log Analytics и платить за каждый гигабайт, принятый службой, или перейти на уровень Insight and Analytics и платить за каждый узел под управлением службы. Insight and Analytics предлагает превосходные возможности, доступные благодаря Log Analytics. Решение мониторинга HBase доступно и с Log Analytics, и с Insight and Analytics.

При подготовке решения Мониторинга HDInsight HBase создается рабочая область Log Analytics. Каждую рабочую область можно представить как уникальную среду Log Analytics со своим собственным репозиторием данных, источниками данных и решениями. В подписке можно создать сразу несколько рабочих областей для поддержки нескольких сред, например рабочей среды и среды тестирования.

## <a name="provision-hdinsight-hbase-monitoring"></a>Подготовка мониторинга HDInsight HBase

1. Войдите на [портал Azure](https://portal.azure.com), используя свою подписку Azure.
2. В области **Создать** выберите **Marketplace**, а затем — **Мониторинг и управление**.
3. В области **Мониторинг и управление** щелкните **Просмотреть все**.

    ![Область "Мониторинг и управление"](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. В списке найдите раздел **Решения для управления**. Справа от заголовка **Решения для управления** щелкните **More** (Дополнительные).

    ![Область "Мониторинг и управление"](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. В области **Решения для управления** выберите решение по управлению "Мониторинг HDInsight HBase", чтобы добавить его в рабочую область.

    ![Область "Решения для управления"](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. В области решений для управления просмотрите сведения о решении, а затем выберите **Создать**. 
7. В области *имени решения для управления* выберите имеющуюся рабочую область, чтобы связать ее с решением для управления, или создайте новую рабочую область Log Analytics и выберите ее.
8. Измените параметры подписки Azure, группы ресурсов и расположения для рабочей области соответствующим образом. 
    ![Рабочая область решения](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Нажмите кнопку **Создать**.  
10. Чтобы использовать это решение для управления в рабочей области, выберите **Log Analytics** > ***имя рабочей области*** > **Решения**. В списке появится запись для вашего решения для управления. Выберите ее, чтобы перейти к решению.

    ![Решения Log Analytics](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. Появится область для решения мониторинга HDInsight HBase.

    ![Область решений HDInsight HBase](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Плитки в разделе "Сводка" не отображают никаких данных, так как кластер HDInsight HBase еще не был настроен для отправки данных в Log Analytics.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Подключение кластера HDInsight HBase к Log Analytics

Чтобы использовать средства, предоставляемые решением мониторинга HDInsight HBase, в кластере необходимо настроить передачу метрик с сервера региона, головных узлов и узлов ZooKeeper в Log Analytics. Для этого необходимо запустить действие скрипта для кластера HDInsight HBase.

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>Получение идентификатора и ключа рабочей области Log Analytics

Чтобы выполнить для узлов в кластере аутентификацию в Log Analytics, необходимы идентификатор и ключ рабочей области Log Analytics. Для получения этих значений:

1. В области мониторинга HBase на портале Azure выберите "Обзор".

    ![Область решения мониторинга HBase](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Выберите "Портал OMS", чтобы запустить портал OMS в новой вкладке или в новом окне браузера.

    ![Выбор элемента "Портал OMS"](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. На домашней странице портала OMS выберите "Параметры".

    ![Портал OMS](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Выберите Connected Source (Подключенный источник), а затем "Серверы с Linux".

    ![Элементы Connected Source (Подключенный источник) и "Серверы с Linux"](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Обратите внимание, что отображаются значения идентификатора и первичного ключа рабочей области, так как эти значения нужны для действия скрипта.

### <a name="run-the-script-action"></a>Выполнение действия скрипта

Чтобы включить сбор данных из кластера HDInsight HBase, выполните действие скрипта на всех узлах кластера:

1. Перейдите в область кластера HDInsight HBase на портале Azure.
2. Выберите **Действия скрипта**.

    ![Элемент "Действия скрипта"](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Выберите **Отправить новое**.

    ![Элемент "Отправить новое"](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. В действии **Отправить скрипт** задайте тип скрипта `"- Custom"`.
5. Укажите имя этого скрипта.
6. В поле **URI bash-скрипта** вставьте следующий URI:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. В разделе **Типы узлов** выберите все три (**Head** (Головной), **Регион**, **Zookeeper**).
8. В текстовом поле **Параметры** введите идентификатор и ключ рабочей области, заключив каждое значение в кавычки и разделив их пробелом.

        "<Workspace ID>" "<Workspace Key>"

9. Установите флажок **Сохранить этот скрипт для повторного выполнения при добавлении новых узлов в кластер**.

    ![Параметры действия скрипта](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Нажмите кнопку **Создать**.
11. Выполнение этого действия скрипта занимает несколько минут. Вы можете отслеживать его состояние на панели "Действия скрипта".

    ![Действие скрипта выполняется](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. По завершении действия скрипта вы увидите зеленый флажок рядом с именем скрипта в списке.

    ![Действие скрипта завершено](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Просмотр решения мониторинга HDInsight HBase

По завершении действия скрипта в решении мониторинга через несколько минут отобразятся данные.

1. На портале Azure перейдите в область решения HDInsight HBase.
2. Выберите вкладку **Обзор**.
3. В разделе **Сводка** вы увидите плитку, где показано количество региональных серверов, которые отслеживаются.

    ![Плитка в разделе сводки решения мониторинга](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Выберите плитку с числом региональных серверов. Отобразится основная панель мониторинга.
5. Эта панель мониторинга предоставляет доступ к статистике по регионам, используемому счетчику упреждающего протоколирования, коллекции выполненных поисков Log Analytics (по журналам региональных серверов, журналам Phoenix и исключениям) и коллекции популярных диаграмм для визуализации применимых метрик. 

    ![Основная панель мониторинга](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. Если выбрать один из этих разделов, откроется подробное представление поиска по журналам, где можно уточнить запрос и более детально рассмотреть данные.

## <a name="next-steps"></a>Дополнительная информация

* [Создание оповещений в Log Analytics](../../log-analytics/log-analytics-alerts-creating.md)
* [Поиск данных по журналам в Log Analytics](../../log-analytics/log-analytics-log-searches.md)
