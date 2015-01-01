<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="Выполнение учебных задач Hadoop в Azure HDInsight" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Get started using the Azure HDInsight service with the samples provided. Use PowerShell scripts that run MapReduce programs on data clusters." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the Hadoop samples in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#Выполнение примеров Hadoop в HDInsight

Предоставляется ряд примеров, которые помогут начать работу с заданиями MapReduce на кластерах Hadoop с использованием Azure HDInsight. Эти примеры доступны на каждом создаваемом управляемом кластере HDInsight. Выполнение примеров позволит познакомиться с использованием командлетов Azure PowerShell HDInsight для запуска заданий на кластерах Hadoop.

Программы MapReduce также могут выполняться программно из приложения с использованием интерфейса API Microsoft .NET для HDInsight. Дополнительную информацию об использовании интерфейсов API HDInsight для отправки заданий см. в разделе [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs].

В Интернете есть большой объем дополнительной документации по технологиям, связанным с Hadoop, например программирование и потоковая передачи MapReduce на основе Java, а также документация по командлетам, используемым в сценариях PowerShell. Дополнительную информацию об этих ресурсах см. в итоговом разделе **Ресурсы для HDInsight** темы [Введение в Azure HDInsight][hdinsight-introduction].

**Что представляют собой эти примеры**

<p>Эти примеры предназначены для быстрого ознакомление с развертыванием заданий Hadoop и предоставления основы для расширенного тестирования при работе с концепциями и сценариями процедур, используемых службой. Они предоставляют примеры типичных задач, таких как создание и импорт наборов данных различных размеров, выполнение заданий и последовательное составление заданий, а также просмотра результатов выполнения заданий. Используемые наборы данных могут различаться по размеру, что позволяет отслеживать воздействие этих наборов данных различных размеров на производительность заданий.</p>


**Предварительные требования**:	

- Необходимо иметь учетную запись Azure. Возможности регистрации учетной записи см. в разделе [Бесплатное пробное использование Azure].(http://azure.microsoft.com/ru-ru/pricing/free-trial/) .

- Вы должны были подготовить кластер HDInsight. Сведения по различным способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight](../hdinsight-provision-clusters/)

- Необходимо установить Azure PowerShell и настроить эту среду для поддержки вашей учетной записи. Указания по выполнению этой задачи см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

## Примеры

HDInsight поставляется вместе со следующими примерами.

- [**Пример оценки числа пи**][hdinsight-sample-pi-estimator] В этом учебнике демонстрируется, как создать программу MapReduce с HDInsight, в которой используется статистический метод (квази-Монте-Карло) для оценки числа пи.
- [**Пример счетчика слов**][hdinsight-sample-wordcount] В этом учебнике демонстрируется, как использовать кластер HDInsight для выполнения программы MapReduce, которая подсчитывает количество вхождений слова в текстовом файле.
- [**Пример сортировки Graysort 10 ГБ**][hdinsight-sample-10gb-graysort] В этом учебнике показано, как запускать сортировку GraySort общего назначения для 10-ГБ файла с помощью HDInsight. Необходимо выполнить три задания: Teragen для создания данных, Terasort для сортировки данных и Teravalidate для подтверждения правильности сортировки данных.
- [**Пример потоковой передачи, написанный на C#**][hdinsight-sample-csharp-streaming] В этом учебнике показано, как использовать C# для написания программы MapReduce, в которой используется интерфейс потоковой передачи Hadoop. 


## Как выполнять примеры

Примеры можно выполнить с помощью Azure PowerShell. Указания по выполнению сопровождают все примеры на указанных выше страницах.

##Дальнейшие действия

В этой статье, а также в статьях, сопровождающих каждый пример, вы узнаете, как выполнять примеры, сопровождающие каждый кластер HDInsight, с помощью Azure PowerShell. Учебники по использованию Pig, Hive и MapReduce с HDInsight см. в следующих разделах:

* [Начало работы со службой Azure HDInsight ][hdinsight-get-started]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]
* [Документация по пакету SDK для Azure для HDInsight][hdinsight-sdk-documentation]
* [Отладка HDInsight: сообщения об ошибках][hdinsight-errors]


[hdinsight-errors]: ../hdinsight-debug-jobs/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-introduction]: ../hdinsight-hadoop-introduction/



[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=35_1-->
