---
title: "Использование средств визуализации данных с помощью Spark BI в Azure HDInsight | Документация Майкрософт"
description: "Используйте средства визуализации данных для аналитики с помощью Apache Spark BI в кластерах HDInsight"
keywords: "apache spark bi, spark bi, визуализация данных spark, бизнес-аналитика spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 074415ba50ecdb1799093a3ead3bdd22fd02cc15
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Использование средств визуализации данных с помощью Apache Spark BI в Azure HDInsight

Узнайте, как использовать [Microsoft Power BI](http://powerbi.microsoft.com) и [Tableau](http://www.tableau.com) для визуализации данных в кластере Apache Spark в Azure HDInsight.

## <a name="prerequisites"></a>предварительным требованиям

* **Выполните инструкции из статьи [Выполнение интерактивных запросов в кластерах Spark в HDInsight](./apache-spark-load-data-run-query.md)**.
* **Для Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) и [пробная подписка Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (необязательно).
* **Для Tableau**: [Tableau Desktop](http://www.tableau.com/products/desktop) и [драйвер Microsoft Spark ODBC](http://go.microsoft.com/fwlink/?LinkId=616229).


## <a name="hivetable"></a>Проверка данных

Записная книжка Jupyter, созданная при работе с [предыдущим руководством](apache-spark-load-data-run-query.md), содержит код для создания таблицы `hvac`. Эта таблица базируется на CSV-файле, доступном во всех кластерах HDInsight Spark в **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Ниже описана процедура проверки данных.

1. В записной книжке Jupyter вставьте приведенный ниже пример кода и нажмите клавиши **SHIFT + ВВОД**. Этот код проверяет существование таблиц.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Он возвращает примерно такие выходные данные:

    ![Отображение таблиц в Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Если закрыть записную книжку перед выполнением шага выше, таблица `hvactemptable` будет очищена и не будет включена в выходные данные.
    С помощью инструментов бизнес-аналитики можно получить доступ только к таблицам Hive, размещенным в хранилище метаданных (для таких таблиц в столбце **isTemporary** задано значение **false**). В этом руководстве вы подключитесь к созданной ранее таблице **hvac**.

2. Вставьте указанный ниже фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. Этот код проверяет данные в таблице.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Он возвращает примерно такие выходные данные:

    ![Отображение строк из таблицы hvac в Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. В меню **File** (Файл) записной книжки выберите пункт **Close and Halt** (Закрыть и остановить). Завершите работу записной книжки для освобождения ресурсов. 















## <a name="powerbi"></a>Использование Power BI

В этом разделе вы с помощью Power BI создадите визуализации, отчеты и панели мониторинга на основе данных из кластера Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Создание отчета в Power BI Desktop
Начиная работать со Spark, в первую очередь необходимо подключиться к кластеру в Power BI Desktop, загрузить данные из кластера, а затем создать базовую визуализацию на основе этих данных.

> [!NOTE]
> Соединитель, описанный в этой статье, находится в стадии предварительной версии. Любые отзывы о нем вы можете предоставить на [сайте сообщества Power BI](https://community.powerbi.com/) или на форуме [Power BI ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Откройте [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. На вкладке **Home** (Главная) щелкните **Get Data** (Получение данных), а затем **More** (Дополнительно).

    ![Получение данных в Power BI Desktop из HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Get data into Power BI from Apache Spark BI")


2. Введите в поле поиска `Spark` и выберите **Azure HDInsight Spark (бета-версия)**, а затем нажмите кнопку **Connect** (Подключиться).

    ![Получение данных в Power BI из Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Получение данных в Power BI из Apache Spark BI")

3. Введите URL-адрес кластера (в формате `mysparkcluster.azurehdinsight.net`), выберите **DirectQuery** и нажмите кнопку **ОК**.

    Для Spark можно использовать любой режим подключения данных. При использовании DirectQuery изменения отображаются в отчетах, при этом весь набор данных не обновляется. В случае импорта данных необходимо обновить набор данных, чтобы увидеть изменения. Дополнительные сведения о том, как и когда следует использовать DirectQuery, см. в статье [Использование DirectQuery в Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Введите данные учетной записи для входа в HDInsight и нажмите кнопку **Connect** (Подключиться). Имя учетной записи по умолчанию — *admin*.

5. Выберите таблицу `hvac`, дождитесь отображения данных предварительного просмотра и нажмите кнопку **Load** (Загрузить).

    ![Имя пользователя и пароль для кластера Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Имя пользователя и пароль для кластера Spark")

    Теперь в Power BI Desktop есть все сведения, необходимые для подключения к кластеру Spark и загрузки данных из таблицы `hvac`. Таблица и ее столбцы отображаются в области **Fields** (Поля).  Экран должен выглядеть так:

6. Отобразите расхождения между целевой температурой и фактической температурой для каждого здания: 

    1. В области **визуализаций** выберите **Диаграмма с областями**. 
    2. Перетащите поле **BuildingID** в раздел **Ось**, а поля **ActualTemp** и **TargetTemp** в раздел **значений**.

        ![Создание визуализаций данных Spark с помощью Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Создание визуализаций данных Spark с помощью Apache Spark BI")

        Схема выглядит следующим образом.

        ![Создание визуализаций данных Spark с помощью Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Создание визуализаций данных Spark с помощью Apache Spark BI")

        По умолчанию представление показывает сумму для **фактической температуры** и **целевой температуры**. Щелкните направленную вниз стрелку рядом с полями **ActualTemp** и **TragetTemp** на панели визуализации, и вы увидите выбранную **сумму**.

    3. Щелкните направленную вниз стрелку рядом с полями **ActualTemp** и **TragetTemp** на панели визуализации, выберите **Average** (Среднее), чтобы получить среднее значение фактической и целевой температуры для каждого здания.

        ![Создание визуализаций данных Spark с помощью Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Создание визуализаций данных Spark с помощью Apache Spark BI")

        Визуализация данных должна выглядеть примерно так, как показано на снимке экрана ниже. Наведите указатель мыши на визуализацию, чтобы отобразить всплывающие подсказки с соответствующими данными.

        ![Создание визуализаций данных Spark с помощью Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Создание визуализаций данных Spark с помощью Apache Spark BI")

7. Щелкните **Файл**, **Сохранить**, а затем введите имя файла `BuildingTemperature.pbix`. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Публикация отчета в службе Power BI (необязательно)

Служба Power BI поддерживает совместное использование отчетов и панелей мониторинга для всей организации. В этом разделе вы опубликуете набор данных и отчет. Затем вы закрепите отчет на панели мониторинга. Информационные панели обычно используются, чтобы сосредоточиться на подмножестве данных в отчете. В вашем отчете содержится только одна визуализация, но тем не менее выполнение шагов ниже по-прежнему актуально.

1. Откройте Power BI Desktop.
2. На вкладке **Home** (Главная) щелкните **Publish** (Опубликовать).

    ![Публикация из Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Публикация из Power BI Desktop")

2. Выберите рабочую область, в которой вы хотите опубликовать набор данных и отчет, а затем нажмите кнопку **Выбор**. На рисунке ниже выбран параметр по умолчанию **Моя рабочая область**.

    ![Выбор рабочей области для публикации набора данных и отчета](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Выбор рабочей области для публикации набора данных и отчета") 

3. Когда публикация успешно завершится, нажмите кнопку **Open 'BuildingTemperature.pbix' in Power BI** (Открыть BuildingTemperature.pbix в Power BI).

    ![Публикация завершилась успешно, щелкните, чтобы ввести учетные данные](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Публикация завершилась успешно, щелкните, чтобы ввести учетные данные") 

4. В службе Power BI щелкните ссылку, чтобы **ввести учетные данные**.

    ![Ввод учетных данных в службе Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Ввод учетных данных в службе Power BI")

5. Щелкните ссылку, чтобы **изменить учетные данные**.

    ![Изменение учетных данных в службе Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Изменение учетных данных в службе Power BI")

6. Введите данные учетной записи для входа в HDInsight и щелкните **Sign in** (Вход). Имя учетной записи по умолчанию — *admin*.

    ![Вход в кластер Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Вход в кластер Spark")

7. В левой области выберите **Рабочие области** > **Моя рабочая область** > **Отчеты**, а затем щелкните **BuildingTemperature**.

    ![Отчет в списке раздела отчетов в левой области](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Отчет в списке раздела отчетов в левой области")

    Вы также увидите **BuildingTemperature** в списке раздела **Наборы данных** в левой области.

    Визуальный элемент, созданный в Power BI Desktop, теперь доступен в службе Power BI. 

8. Наведите курсор на визуализацию и щелкните значок булавки в правом верхнем углу.

    ![Отчет в службе Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Отчет в службе Power BI")

9. Выберите "Новая информационная панель", введите имя `Building temperature`, а затем нажмите кнопку **Pin** (Закрепить).

    ![Закрепление на новой информационной панели](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Закрепление на новой информационной панели")

10. В отчете нажмите кнопку **Перейти к информационной панели**. 

Ваш визуальный элемент закреплен на информационной панели. Вы можете добавить в отчет другие визуальные элементы, а затем закрепить их на этой же информационной панели. Дополнительные сведения об отчетах и информационных панелях см. в статьях [Отчеты в Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) и [Панели мониторинга в службе Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="tableau"></a>Использование Tableau Desktop 

> [!NOTE]
> Этот раздел применим только для кластеров Spark 1.5.2, созданных в Azure HDInsight.
>
>

1. Установите [Tableau Desktop](http://www.tableau.com/products/desktop) на компьютере, на котором вы выполняете действия из этого руководства по Apache Spark BI.

2. Убедитесь, что на этом компьютере также установлен драйвер ODBC Microsoft Spark. Вы можете скачать драйвер [здесь](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Запустите Tableau Desktop. В левой области в списке сервера для подключения щелкните **Spark SQL**. Если Spark SQL по умолчанию не отображается в левой области, вы можете найти его, щелкнув **Другие серверы**.
2. В диалоговом окне подключения Spark SQL укажите значения, показанные на снимке экрана ниже, и нажмите кнопку **ОК**.

    ![Подключение к кластеру для Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Подключение к кластеру для Apache Spark BI")

    Если вы установили на компьютер **драйвер Microsoft ODBC Spark** , в раскрывающемся списке проверки подлинности будет пункт [служба Microsoft Azure HDInsight](http://go.microsoft.com/fwlink/?LinkId=616229) .
3. В следующем окне в раскрывающемся списке **Схема** щелкните значок **Найти**, а затем щелкните **по умолчанию**.

    ![Поиск схемы для Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Поиск схемы для Apache Spark BI")
4. Для поля **Таблица** еще раз щелкните значок **Найти**, чтобы вывести список всех таблиц Hive, доступных в кластере. Вы должны увидеть таблицу **hvac** , созданную ранее с помощью записной книжки.

    ![Поиск схемы для Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Поиск схемы для Apache Spark BI")
5. Перетащите таблицу в поле в правом верхней части окна. Tableau импортирует данные и отображает схему (выделено красным прямоугольником).

    ![Добавление таблиц в Tableau для Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Добавление таблиц в Tableau для Apache Spark BI")
6. Щелкните вкладку **Лист1** слева внизу. Создайте визуализацию, на которой представлены средняя целевая температура фактическая температура для всех зданий для каждой даты. Перетащите поля **Дата** и **Код здания** в раздел **Столбцы**, а поля **Actual Temp**(Фактическая температура)/**Target Temp** (Целевая температура) — в раздел **Строки**. В разделе **Метки** выберите **Область**, которая будет использоваться для визуализации данных Spark.

     ![Добавление полей для визуализации данных Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Добавление полей для визуализации данных Spark")
7. По умолчанию значения полей температуры отображаются как статистическое выражение. Если вы хотите отобразить среднюю температуру, это можно сделать с помощью раскрывающегося списка, как показано на следующем снимке экрана.

    ![Получение среднего значения температуры для визуализации данных Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Получение среднего значения температуры для визуализации данных Spark")

8. Можно также наложить одну карту температуры на другую, чтобы лучше понять разницу между целевой и фактической температурой. Перемещайте указатель мыши в угол нижней области карты, пока курсор не примет форму красного кружка. Перетащите карту на другую карту в верхней части и отпустите кнопку мыши, когда указатель мыши примет форму красного прямоугольника.

    ![Слияние карт для визуализации данных Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Слияние карт для визуализации данных Spark")

     Визуализация данных должна измениться, как показано на снимке экрана.

    ![Выходные данные Tableau для визуализации данных Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Выходные данные Tableau для визуализации данных Spark")
9. Щелкните **Сохранить** для сохранения рабочего листа. Можно создать панели мониторинга и добавить на них один или несколько листов.

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как создать кластер, как создать кадры данных Spark для запроса данных и как получить доступ к этим данным из средств бизнес-аналитики. Теперь вы можете просмотреть инструкции по управлению ресурсами кластера и отладке заданий, запущенных в кластере Spark в HDInsight.

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

