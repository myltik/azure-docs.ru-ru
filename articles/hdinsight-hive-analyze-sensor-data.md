<properties title="Analyzing sensor data using Hive with HDInsight" pageTitle="Анализ данных датчика с помощью Hive и Microsoft Azure HDInsight (Hadoop)" description="Узнайте, как анализировать и визуализировать данные датчика с HDInsight (Hadoop), используя Hive и Excel" metaKeywords="Azure hdinsight hive, Azure hdinsight hive sensor, azure hadoop hive, azure hadoop sensor, azure hadoop excel, azure hdinsight excel" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

#Анализ данных датчика с помощью Hive с HDInsight

Узнайте, как анализировать данные датчика с помощью Hive с HDInsight (Hadoop), а затем визуализировать данные в Microsoft Excel с помощью Power View.

В этом примере мы будем использовать Hive для обработки исторических данных, полученных системами отопления, вентиляции и кондиционирования воздуха, чтобы определить системы, которые не в состоянии надежно поддерживать заданную температуру. Вы научитесь:

- создавать HIVE таблицы для формирования запросов к данным, хранящимся в файлах значений, разделенных запятыми (CSV-файлах);
- создавать HIVE запросы для анализа данных;
- использовать Microsoft Excel для подключения к HDInsight (с помощью соединения ODBC), чтобы извлекать проанализированные данные;
- использовать Power View для визуализации данных.

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

##Предварительные требования:

* Кластер HDInsight (Hadoop) - см. раздел [Подготовка кластеров Hadoop в HDInsight](/ru-ru/documentation/articles/hdinsight-provision-clusters/) для получения информации о создании кластера

* Microsoft Excel 2013

	> [WACOM.NOTE] Microsoft Excel используется для визуализации данных с помощью службы [Power View](https://support.office.com/en-US/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US), которая в настоящее время доступна только в Windows.

* [Драйвер Microsoft Hive ODBC](http://www.microsoft.com/ru-ru/download/details.aspx?id=40886)

##Запуск образца

1. На портале управления Azure щелкните кластер, на котором необходимо запустить образец, а затем внизу выберите пункт **Консоль запросов**. Как вариант, консоль запросов можно открыть напрямую с помощью следующего URL-адреса

	 	https://<clustername>.azurehdinsight.net

	В ответ на запрос выполните аутентификацию с помощью имени пользователя и пароля администратора, использованных при подготовке данного кластера.

2. На открывшейся веб-странице выберите вкладку **Начало работы с коллекцией**, а затем в категории **Образцы** щелкните образец **Анализ журнала веб-сайта**.

3. Следуйте инструкциям, представленным на веб-странице, чтобы закончить образец.

<!--HONumber=35_1-->
