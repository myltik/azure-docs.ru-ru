---
title: Установка опубликованного приложения H2O Sparkling Water в Azure HDInsight | Документация Майкрософт
description: Установка и использование стороннего приложения Hadoop H2O Sparkling Water.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 9a03588b3327c3ab231f5c2cae17488f4d63bde7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---h2o-sparkling-water"></a>Установка опубликованного приложения H2O Sparkling Water

В этой статье описывается, как установить и запустить опубликованное приложение Hadoop [H20 Sparkling Water](http://www.h2o.ai/) в Azure HDInsight. Обзор платформы приложений HDInsight и список доступных опубликованных приложений независимых поставщиков программного обеспечения приведены в статье [Установка сторонних приложений Hadoop в Azure HDInsight](hdinsight-apps-install-applications.md). Инструкции по установке собственного приложения см. в статье [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>Об H2O Sparkling Water

H2O Sparkling Water — полностью распределенная, хранящаяся в памяти платформа обучения с открытым кодом, обеспечивающая линейную масштабируемость. H2O Sparkling Water позволяет объединять быстрые масштабируемые алгоритмы машинного обучения H2O с возможностями Spark. Используя Sparkling Water, пользователи могут обрабатывать вычисления из Scala, R и Python с помощью пользовательского интерфейса H2O Flow.

H2O Sparkling Water обеспечивает:

* **Удобный пользовательский веб-интерфейс и привычные интерфейсы**. Быстрая настройка и начало работы с помощью интуитивно понятного графического пользовательского веб-интерфейса или сред программирования, например R, Python, Java, Scala, JSON, а также интерфейсов API H2O.
* **Независимая от данных поддержка всех распространенных баз данных и типов файлов**. Удобный просмотр и моделирование больших данных из Microsoft Excel, R Studio, Tableau и пр. Подключение к данным из источников данных HDFS, S3, SQL и NoSQL.
* **Высокомасштабируемое необратимое преобразование и анализ больших данных**. Операции соединения больших данных H2O могут выполняться в 7 раз быстрее, чем операции R data.table, и их можно линейно масштабировать до "10 миллиардов x 10 миллиардов" соединений строк.
* **Оценка данных в реальном времени**. Быстрое развертывание моделей в рабочей среде с помощью простых объектов Java (POJO), оптимизированных для моделей объектов Java (MOJO) или REST API H2O.

### <a name="resource-links"></a>Ссылки на ресурсы

* [Стратегия разработки H2O.ai](https://jira.h2o.ai/)
* [Домашняя страница H2O.ai](http://www.h2o.ai/)
* [Документация по H2O.ai](http://docs.h2o.ai/)
* [Служба поддержки H2O.ai](https://support.h2o.ai/)
* [База открытого кода H2O.ai](https://github.com/h2oai/)

## <a name="prerequisites"></a>предварительным требованиям

Для установки этого приложения на новый кластер HDInsight или на имеющийся кластер требуется следующая конфигурация:

* уровни кластера: "Стандартный" или "Премиум";
* тип кластера: Spark;
* версии кластера: 3.5 или 3.6.

## <a name="install-the-h2o-sparkling-water-published-application"></a>Установка опубликованного приложения H2O Sparkling Water

Пошаговые инструкции по установке этого и других доступных приложений независимых поставщиков программного обеспечения см. в статье [Установка сторонних приложений Hadoop в Azure HDInsight](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Запуск H2O Sparkling Water

1. После установки можно приступить к использованию H2O Sparkling Water (h2o-sparklingwater) в кластере на портале Azure, открыв записные книжки Jupyter (`https://<ClusterName>.azurehdinsight.net/jupyter`). Кроме того, перейти к Jupyter можно, выбрав **панель мониторинга кластера** в области своего кластера на портале и щелкнув **Записная книжка Jupyter**. Появится запрос на ввод учетных данных. Введите учетные данные кластера Hadoop, указанные при его создании.

2. В Jupyter отображаются три папки: H2O-PySparkling-Examples, PySpark Examples и Scala Examples. Выберите папку **H2O-PySparkling-Examples**.

    ![Домашняя страница записных книжек Jupyter](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Первым шагом при создании записной книжки является настройка среды Spark. Эти сведения приведены в примере **Sentiment_analysis_with_Sparkling_Water**. При настройке среды Spark обязательно используйте правильный JAR-файл и укажите IP-адрес, полученный из выходных данных первой ячейки.

    ![Домашняя страница записных книжек Jupyter](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Запустите кластер H2O.

    ![Запуск кластера](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. После настройки и запуска кластера H2O откройте H2O Flow, перейдя по адресу **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Если вам не удалось открыть H2O Flow, попробуйте очистить кэш браузера. Если вам по-прежнему не удается получить доступ к H2O Flow, возможно, в кластере недостаточно ресурсов. Попробуйте увеличить число рабочих узлов в разделе **Изменить масштаб кластера** в области кластера.

    ![Панель мониторинга H2O Flow](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. В меню справа выберите пример **Million_Songs.flow**. Когда отобразится предупреждение, щелкните **Load Notebook** (Загрузить записную книжку). Эта демоверсия предназначена для работы с реальными данными в течение нескольких минут. Цель состоит в том, чтобы на основе данных с помощью двоичной классификации определить, была ли песня выпущена до или после 2004 года.

    ![Выбор примера Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Найдите путь, который содержит **milsongs-cls-train.csv.gz**, и замените весь путь значением **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Найдите путь, который содержит **milsongs-cls-test.csv.gz**, и замените его значением **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Чтобы выполнить все инструкции в ячейках записной книжки, на панели инструментов нажмите кнопку **Run All** (Запустить все).

    ![Запустить все](./media/hdinsight-apps-install-h2o/run-all.png)

10. Через несколько минут должен отобразиться результат, аналогичный приведенному ниже.

    ![Выходные данные](./media/hdinsight-apps-install-h2o/output.png)

Вот и все! В течение нескольких минут вы использовали искусственный интеллект в Spark. Теперь вы можете изучить дополнительные примеры в H2O Flow, в которых представлены различные типы алгоритмов машинного обучения.

## <a name="next-steps"></a>Дополнительная информация

* [Документация по H2O](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Установка пользовательских приложений Hadoop в Azure HDInsight](hdinsight-apps-install-custom-applications.md) — узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
* [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md)— узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
* [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(Установка приложения HDInsight) — узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md) — узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](hdinsight-apps-use-edge-node.md) — узнайте, как использовать пустой граничный узел для доступа к кластерам HDInsight, а также тестирования и размещения приложений HDInsight.
