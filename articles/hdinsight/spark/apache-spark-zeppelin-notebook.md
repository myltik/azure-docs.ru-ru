---
title: Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight | Документация Майкрософт
description: Пошаговые инструкции по использованию записных книжек Zeppelin с кластерами Apache Spark в Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b2f47dce058af7a39366c06d0b33117a66ed116a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522010"
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight

Кластеры Spark HDInsight включают в себя записные книжки Zeppelin, которые можно использовать для выполнения заданий Spark. Из этой статьи вы узнаете, как использовать записную книжку Zeppelin в кластере HDInsight.

> [!NOTE]
> Записные книжки Zeppelin доступны только для Spark 1.6.3 в HDInsight 3.5 и для Spark 2.1.0 в HDInsight 3.6.
>

**Предварительные требования:**

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Запуск записной книжки Zeppelin
1. В колонке кластера Spark щелкните **Панель мониторинга кластера**, а затем выберите **Записная книжка Zeppelin**. При появлении запроса введите учетные данные администратора для кластера.
   
   > [!NOTE]
   > Также можно открыть Zeppelin Notebook для своего кластера, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. Создайте новую записную книжку. На панели заголовка щелкните элемент **Notebook** (Записная книжка), а затем — **Create New Note** (Создать заметку).
   
    ![Создание записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Создание записной книжки Zeppelin")
   
    Введите имя для записной книжки и щелкните **Создать заметку**.
3. Кроме того, убедитесь, что в заголовке записной книжки отображается состояние "Подключено". Оно обозначается зеленой точкой в правом верхнем углу.
   
    ![Состояния записной книжки Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Состояния записной книжки Zeppelin")
4. Загрузите демонстрационные данные во временную таблицу. При создании кластера Spark в HDInsight файл с демонстрационными данными **hvac.csv** копируется в связанную учетную запись хранения по следующему пути: **\HdiSamples\SensorSampleData\hvac**.
   
    В пустой абзац, созданный по умолчанию в новой записной книжке, вставьте следующий фрагмент кода.
   
        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
   
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
   
    Нажмите клавиши **SHIFT+ВВОД** или кнопку **воспроизведения** для абзаца, чтобы выполнить фрагмент кода. Состояние, которое отображается в правом верхнем углу абзаца, должно изменяться в следующей последовательности: READY (ГОТОВО), PENDING (ОЖИДАЕТ), RUNNING (ВЫПОЛНЯЕТСЯ) и FINISHED (ЗАВЕРШЕНО). Выходные данные отображаются в нижней части того же абзаца. Снимок экрана выглядит следующим образом:
   
    ![Создание временной таблицы из необработанных данных](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Создание временной таблицы из необработанных данных")
   
    Можно указать заголовок для каждого абзаца. В правом верхнем углу экрана щелкните значок **Параметры**, а затем выберите элемент **Показать заголовок**.
5. Теперь вы можете выполнить инструкции Spark SQL для таблицы **hvac** . Вставьте следующий запрос в новый абзац. Запрос извлекает идентификатор здания и разницу между целевой и фактической температурами для каждого здания в указанный день. Нажмите **SHIFT + ВВОД**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    Инструкция **%sql** в начале сообщает записной книжке, что необходимо использовать интерпретатор Livy Scala.
   
    Выходные данные показаны на снимке экрана ниже.
   
    ![Выполнение инструкции Spark SQL с помощью записной книжки](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Выполнение инструкции Spark SQL с помощью записной книжки")
   
     Используйте параметры отображения (выделены красным прямоугольником) для переключения между различными представлениями одних и тех же выходных данных. Щелкните элемент **Параметры** , чтобы определить ключи и значения в выходных данных. На снимке экрана выше параметр **buildingID** используется в качестве ключа, а среднее значение **temp_diff** — как значение.
6. Можно также запустить инструкции Spark SQL с помощью переменных в запросе. В следующем фрагменте кода показано, как определить переменную ( **Temp**) в запросе с возможными значениями, с которыми необходимо выполнить запрос. При первом выполнении запроса раскрывающийся список автоматически заполняется значениями, указанными для переменной.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Вставьте этот фрагмент кода в новый абзац и нажмите клавиши **SHIFT + ВВОД**. Выходные данные показаны на снимке экрана ниже.
   
    ![Выполнение инструкции Spark SQL с помощью записной книжки](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Выполнение инструкции Spark SQL с помощью записной книжки")
   
    Для последующих запросов можно выбрать новое значение из раскрывающегося списка и повторно выполнить запрос. Щелкните элемент **Параметры** , чтобы определить ключи и значения в выходных данных. На снимке экрана выше параметр **buildingID** используется в качестве ключа, среднее значение **temp_diff** используется как значение, а **targettemp** — как группа.
7. Перезапустите интерпретатор Livy, чтобы выйти из приложения. Для этого откройте параметры интерпретатора: щелкните имя вошедшего в систему пользователя в правом верхнем углу и нажмите кнопку **Interpreter** (Интерпретатор).
   
    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")
8. Прокрутите до параметров интерпретатора Livy и выберите **Перезапустить**.
   
    ![Перезапуск интерпретатора Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Перезапуск интерпретатора Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Использование внешних пакетов с записной книжкой
Вы можете настроить записную книжку Zeppelin в кластере Apache Spark в HDInsight (Linux) для использования внешних, предоставленных сообществом пакетов, которые не включены в готовую версию кластера. Полный список доступных пакетов можно найти в [репозитории Maven](http://search.maven.org/) . Его также можно получить из других источников. Например, полный список предоставленных сообществом пакетов можно найти в разделе [Пакеты Spark](http://spark-packages.org/).

В этой статье показано, как использовать пакет [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) с записной книжкой Jupyter.

1. Откройте параметры интерпретатора. В правом верхнем углу щелкните имя вошедшего в систему пользователя и выберите **Interpreter** (Интерпретатор).
   
    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")
2. Прокрутите до параметров интерпретатора Livy и выберите **Изменить**.
   
    ![Изменение параметров интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Изменение параметров интерпретатора")
3. Добавьте новый ключ с именем **livy.spark.jars.packages** и присвойте ему значение в формате `group:id:version`. Если вы хотите использовать пакет [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), для ключа необходимо задать значение `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Изменение параметров интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Изменение параметров интерпретатора")
   
    Нажмите кнопку **Сохранить**, а затем перезапустите интерпретатор Livy.
4. **Подсказка.** Вот как можно получить значение указанного выше ключа.
   
    a. Найдите пакет в репозитории Maven. В этом руководстве мы используем [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    Б. В репозитории найдите значения для параметров **GroupId**, **ArtifactId** и **Version**.
   
    ![Использование внешних пакетов с записными книжками Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Использование внешних пакетов с записными книжками Jupyter")
   
    c. Объедините три значения, разделив их двоеточием (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Место сохранения записных книжек Zeppelin
Записные книжки Zeppelin сохраняются на головных узлах кластера. Поэтому при удалении кластера записные книжки также будут удалены. Если вы хотите сохранить записные книжки для последующего использования в других кластерах, необходимо экспортировать их после выполнения заданий. Чтобы экспортировать записную книжку, щелкните значок **Экспорт**, как показано на рисунке ниже.

![Скачивание записной книжки](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Скачивание записной книжки")

Это действие сохраняет записную книжку в формате JSON в расположение для скачивания.

## <a name="livy-session-management"></a>Управление сеансом Livy
При выполнении первого абзаца кода в записной книжке Zeppelin в кластере HDInsight Spark создается новый сеанс Livy. Этот сеанс будет общим в записных книжках Zeppelin, которые вы затем создадите. Если по какой-то причине сеанс Livy будет завершен (перезагрузка кластера и т. д.), вы не сможете запускать задания из записной книжки Zeppelin.

В этом случае перед началом выполнения заданий из записной книжки Zeppelin необходимо сделать следующее: 

1. Перезапустите интерпретатор Livy из записной книжки Zeppelin. Для этого откройте параметры интерпретатора: щелкните имя вошедшего в систему пользователя в правом верхнем углу и нажмите кнопку **Interpreter** (Интерпретатор).
   
    ![Запуск интерпретатора](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Выходные данные Hive")
2. Прокрутите до параметров интерпретатора Livy и выберите **Перезапустить**.
   
    ![Перезапуск интерпретатора Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Перезапуск интерпретатора Zeppelin")
3. Запустите ячейку кода из имеющейся записной книжки Zeppelin. При этом в кластере HDInsight будет создан сеанс Livy.

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely on HDInsight Spark Linux cluster (Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







