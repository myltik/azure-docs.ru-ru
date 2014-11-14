<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hive in HDInsight Hadoop for website log analysis" pageTitle="Использование Hive в HDInsight Hadoop для анализа журнала веб-сайта| Azure" metaKeywords="" description="Узнайте, как использовать Hive с HDInsight для анализа журналов веб-сайтов. Вы будете использовать файл журнала в качестве входных данных для таблицы HDInsight, а также HiveQL для формирования запроса к данным." metaCanonical="" services="hdinsight" documentationCenter="" title="Использование Hive в HDInsight Hadoop для анализа журнала веб-сайта" authors="nitinme" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="nitinme" />

# Использование Hive в HDInsight для анализа журналов веб-сайтов

Узнайте, как использовать HiveQL с HDInsight для анализа журналов веб-сайта. Анализ журнала веб-сайта можно использовать для разделения аудитории на основе аналогичной деятельности, классификации посетителей сайта по демографическим данным, разыскивания просмотренного ими содержимого, посещенных ими сайтов и так далее.

В данном примере для анализа файлов журнала веб-сайта вы будете использовать кластер HDInsight, чтобы получить представление о дневной частоте посещений веб-сайта из внешних сайтов, а также получить информацию об ошибках веб-сайта, с которыми столкнулись пользователи. Вы научитесь:

-   подключаться к DLOB-объекту хранилища Azure, содержащему файлы журнала веб-сайта;
-   создавать HIVE таблицы для формирования запросов к этим журналам;
-   создавать HIVE запросы для анализа данных;
-   использовать Microsoft Excel для подключения к HDInsight (с помощью соединения ODBC), чтобы извлекать проанализированы данные.

![HDI.Samples.Website.Log.Analysis][HDI.Samples.Website.Log.Analysis]

## Предварительные требования

-   Вы должны были подготовить **кластер HDInsight**. Для получения инструкций см. раздел [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight].
-   У вас должен быть установлен Microsoft Excel 2010 или Microsoft Excel 2013.
-   У вас должен быть [Microsoft Hive ODBC драйвер][Microsoft Hive ODBC драйвер] для импорта данных из Hive в Excel.

## Запуск образца

1.  В портале управления Azure щелкните кластер, на котором необходимо запустить образец, а затем внизу выберите пункт **Консоль запросов**. Как вариант, консоль запросов можно открыть напрямую с помощью следующего URL-адреса:

        https://<clustername>.azurehdinsight.net

    В ответ на запрос выполните аутентификацию с помощью имени пользователя и пароля администратора, использованных при подготовке данного кластера.

2.  На открывшейся веб-странице выберите вкладку **Начало работы с коллекцией**, а затем в категории **Образцы** щелкните образец **Анализ журнала веб-сайта**.
3.  Следуйте инструкциям, представленным на веб-странице, чтобы закончить образец.

## Дальнейшие действия

Попробуйте образец по анализу данных датчиков, используя Azure HDInsight. См. раздел [Анализ данных датчика с помощью Hive с HDInsight][Анализ данных датчика с помощью Hive с HDInsight].

  [HDI.Samples.Website.Log.Analysis]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Microsoft Hive ODBC драйвер]: http://www.microsoft.com/ru-ru/download/details.aspx?id=40886
  [Анализ данных датчика с помощью Hive с HDInsight]: ../hdinsight-use-hive-sensor-data-analysis/
