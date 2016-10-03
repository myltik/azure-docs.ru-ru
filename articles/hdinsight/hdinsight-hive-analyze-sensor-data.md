<properties
	pageTitle="Анализ данных датчика с помощью Hive и Hadoop | Microsoft Azure"
	description="Узнайте, как анализировать данные датчика с помощью консоли запросов Hive с HDInsight (Hadoop), а затем наглядно представлять данные в Microsoft Excel с помощью Power View."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016" 
	ms.author="larryfr"/>

#Анализ данных датчика с помощью консоли запросов Hive с Hadoop в HDInsight

Узнайте, как анализировать данные датчика с помощью консоли запросов Hive с HDInsight (Hadoop), а затем наглядно представлять данные в Microsoft Excel с помощью Power View.

> [AZURE.NOTE] Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Windows.

В этом примере мы будем использовать Hive для обработки исторических данных, полученных системами отопления, вентиляции и кондиционирования воздуха, чтобы определить системы, которые не в состоянии надежно поддерживать заданную температуру. Вы научитесь:

- создавать таблицы HIVE для формирования запросов к данным, хранящимся в файлах значений, разделенных запятыми (CSV-файлах);
- создавать запросы HIVE для анализа данных;
- использовать Microsoft Excel для подключения к HDInsight (с помощью подключения ODBC), чтобы извлекать проанализированные данные;
- использовать Power View для наглядного представления данных.

![Схема архитектуры решения](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##Предварительные требования

* Кластер HDInsight (Hadoop). Для получения информации о создании кластера см. раздел [Подготовка кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md).

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel используется для визуализации данных с помощью [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=ru-RU&rs=ru-RU&ad=US).

* [Драйвер Microsoft Hive ODBC.](http://www.microsoft.com/download/details.aspx?id=40886)

##Запуск образца

1. Откройте веб-браузер и перейдите по следующему URL-адресу: Замените `<clustername>` на имя вашего кластера HDInsight.

	 	https://<clustername>.azurehdinsight.net

	В ответ на запрос выполните аутентификацию с помощью имени пользователя и пароля администратора, использованных при подготовке данного кластера.

2. На открывшейся веб-странице выберите вкладку **Коллекция для начала работы**, а затем в категории **Решения с примером данных** щелкните пример **Анализ данных датчика**.

    ![Рисунок коллекции для начала работы](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Следуйте инструкциям, представленным на веб-странице, чтобы закончить образец.

<!---HONumber=AcomDC_0921_2016-->