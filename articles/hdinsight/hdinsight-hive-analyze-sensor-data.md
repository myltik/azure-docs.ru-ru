---
title: "Анализ данных датчиков с помощью Hive и Hadoop | Документация Майкрософт"
description: "Узнайте, как анализировать данные датчика с помощью консоли запросов Hive с HDInsight (Hadoop), а затем наглядно представлять данные в Microsoft Excel с помощью Power View."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: 58881fd4608deddd8390cf4905b31a57f526e492


---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Анализ данных датчика с помощью консоли запросов Hive с Hadoop в HDInsight
Узнайте, как анализировать данные датчика с помощью консоли запросов Hive с HDInsight (Hadoop), а затем наглядно представлять данные в Microsoft Excel с помощью Power View.

> [!IMPORTANT]
> Шаги, описанные в этом документе, можно применять только к кластерам HDInsight под управлением Windows. Для версий ниже HDInsight 3.4 кластер HDInsight доступен только в Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).


В этом примере мы будем использовать Hive для обработки исторических данных, полученных системами отопления, вентиляции и кондиционирования воздуха, чтобы определить системы, которые не в состоянии надежно поддерживать заданную температуру. Вы научитесь:

* создавать таблицы HIVE для формирования запросов к данным, хранящимся в файлах значений, разделенных запятыми (CSV-файлах);
* создавать запросы HIVE для анализа данных;
* использовать Microsoft Excel для подключения к HDInsight (с помощью подключения ODBC), чтобы извлекать проанализированные данные;
* использовать Power View для наглядного представления данных.

![Схема архитектуры решения](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Предварительные требования
* Кластер HDInsight (Hadoop). Сведения о создании кластера см. в статье [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-provision-clusters.md).
* Microsoft Excel 2013
  
  > [!NOTE]
  > Microsoft Excel используется для визуализации данных с помощью [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).
  > 
  > 
* [Драйвер Microsoft Hive ODBC.](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Запуск образца
1. Откройте веб-браузер и перейдите по следующему URL-адресу: Замените `<clustername>` на имя вашего кластера HDInsight.
   
         https://<clustername>.azurehdinsight.net
   
    В ответ на запрос выполните аутентификацию с помощью имени пользователя и пароля администратора, использованных при подготовке данного кластера.
2. На открывшейся веб-странице выберите вкладку **Getting Started Gallery** (Коллекция для начала работы), а затем в категории **Solutions with Sample Data** (Решения с примером данных) щелкните пример **Анализ данных датчиков**.
   
    ![Рисунок коллекции для начала работы](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)
3. Следуйте инструкциям, представленным на веб-странице, чтобы закончить образец.




<!--HONumber=Jan17_HO3-->


