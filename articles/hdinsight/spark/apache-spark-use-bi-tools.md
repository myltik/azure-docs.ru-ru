---
title: Руководство. Анализ данных Apache Spark с использованием Power BI в Azure HDInsight | Документация Майкрософт
description: Визуализация хранимых данных Spark в кластерах HDInsight с помощью Microsoft Power BI
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: ece0132573f25f4d288309d2e7bb6710f8fd9519
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771173"
---
# <a name="tutorial-analyze-spark-data-using-power-bi-in-hdinsight"></a>Руководство. Анализ данных Spark с использованием Power BI в HDInsight 

Узнайте, как использовать Microsoft Power BI для визуализации данных в кластере Apache Spark в Azure HDInsight.

Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * Визуализация данных Spark с помощью Power BI

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* **Выполните инструкции в [руководстве по загрузке данных и выполнению запросов в кластере Spark в Azure HDInsight](./apache-spark-load-data-run-query.md)**.
* **Для Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) и [пробная подписка Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (необязательно).


## <a name="verify-the-data"></a>Проверка данных

Записная книжка Jupyter, созданная при работе с [предыдущим руководством](apache-spark-load-data-run-query.md), содержит код для создания таблицы `hvac`. Эта таблица базируется на CSV-файле, доступном во всех кластерах HDInsight Spark в **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Ниже описана процедура проверки данных.

1. В записной книжке Jupyter вставьте приведенный ниже пример кода и нажмите клавиши **SHIFT + ВВОД**. Этот код предназначен для проверки наличия таблиц.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Он возвращает примерно такие выходные данные:

    ![Отображение таблиц в Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Если закрыть записную книжку перед выполнением шага выше, таблица `hvactemptable` будет очищена и не будет включена в выходные данные.  С помощью инструментов бизнес-аналитики можно получить доступ только к таблицам Hive, размещенным в хранилище метаданных (для таких таблиц в столбце **isTemporary** задано значение **false**). В этом руководстве вы подключитесь к созданной ранее таблице **hvac**.

2. Вставьте указанный ниже фрагмент кода в пустую ячейку и нажмите клавиши **SHIFT + ВВОД**. Этот код проверяет данные в таблице.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Он возвращает примерно такие выходные данные:

    ![Отображение строк из таблицы hvac в Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. В меню **File** (Файл) записной книжки выберите пункт **Close and Halt** (Закрыть и остановить). Завершите работу записной книжки для освобождения ресурсов. 

## <a name="visualize-the-data"></a>Визуализация данных

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

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

- Визуализация данных Spark с помощью Power BI.

Теперь переходите к следующей статье, в которой объясняется, как перенести зарегистрированные в Spark данные в средство бизнес-аналитики, например в Power BI. 
> [!div class="nextstepaction"]
> [Запуск задания Spark для потоковой передачи](apache-spark-eventhub-streaming.md)

