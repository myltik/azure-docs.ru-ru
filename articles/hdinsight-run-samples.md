<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="Выполнение примеров HDInsight" pageTitle="Выполнение примеров HDInsight | Azure" metaKeywords="" description="Приступая к работе со службой Azure HDInsight, используя приведенные примеры. Использование сценариев PowerShell, которые запускают программы MapReduce в кластерах данных." metaCanonical="" services="hdinsight" documentationCenter="" title="Выполнение примеров HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




#Выполнение примеров HDInsight

Предоставляется ряд примеров, которые помогут начать работу с Azure HDInsight. Эти примеры доступны на каждом создаваемом кластере HDInsight. Выполнение этих примеров приведет к ознакомлению с командлетами Azure PowerShell HDInsight.

Программы MapReduce также могут выполняться программно из приложения с использованием интерфейса API Microsoft .NET для HDInsight. Сведения об использовании интерфейсов API HDInsight для отправки заданий см. в разделе [Отправка заданий Hadoop программными средствами][submit-jobs-programmatically].

В Интернете есть большой объем дополнительной документации по технологиям, связанным с Hadoop, например программирование и потоковая передачи MapReduce на основе Java, а также документация по командлетам, используемым в сценариях PowerShell. Дополнительные сведения об этих ресурсах см. в итоговом разделе **Ресурсы для HDInsight** темы [Введение в Azure HDInsight][hdinsight-resources].

**Что представляют собой эти примеры**

<p>Эти примеры предназначены для быстрого ознакомление с развертыванием заданий Hadoop и предоставления основы для расширенного тестирования при работе с концепциями и сценариями процедур, используемых службой. Они предоставляют примеры типичных задач, таких как создание и импорт наборов данных различных размеров, выполнение заданий и последовательное составление заданий, а также просмотра результатов выполнения заданий. Используемые наборы данных могут различаться по размеру, что позволяет отслеживать воздействие этих наборов данных различных размеров на производительность заданий.</p>


**Предварительные требования**:	

- Необходимо иметь учетную запись Azure. Возможности регистрации учетной записи см. на странице [Бесплатное пробное использование Azure](http://www.windowsazure.com/ru-ru/pricing/free-trial/).

- Вы должны были подготовить кластер HDInsight. Сведения по различным способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight](/ru-ru/manage/services/hdinsight/provision-hdinsight-clusters/)

- Необходимо установить Azure PowerShell и настроить эту среду для поддержки вашей учетной записи. Сведения о выполнении этой задачи см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].

## Примеры ##

HDInsight поставляется вместе со следующими примерами.

- [**Пример оценки числа пи**][pi-estimator] В этом учебнике демонстрируется, как создать программу MapReduce с HDInsight, в которой используется статистический метод (квази-Монте-Карло) для оценки числа пи.
- [**Пример счетчика слов**][wordcount] В этом учебнике демонстрируется, как использовать кластер HDInsight для выполнения программы MapReduce, которая подсчитывает количество вхождений слова в текстовом файле.
- [**Пример сортировки Graysort 10 ГБ**][10gb-graysort] В этом учебнике показано, как запускать сортировку GraySort общего назначения для 10-ГБ файла с помощью HDInsight. Есть три задания для запуска: Teragen для создания данных, Terasort для сортировки данных и Teravalidate для подтверждения правильности сортировки данных.
- [**Пример потоковой передачи, написанный на C#**][cs-streaming] В этом учебнике показано, как использовать C# для написания программы MapReduce, в которой используется интерфейс потоковой передачи Hadoop. 


## Как выполнять примеры ##

Примеры можно выполнить с помощью Azure PowerShell. Инструкции по выполнению сопровождают все примеры на указанных выше страницах.

##Дальнейшие действия ##

В этой статье, а также в статьях, сопровождающих каждый пример, вы узнаете, как выполнять примеры, сопровождающие каждый кластер HDInsight, с помощью Azure PowerShell. Учебники по использованию Pig, Hive и MapReduce с HDInsight см. в следующих разделах:

* [Начало работы со службой Azure HDInsight][getting-started]
* [Использование Pig с HDInsight][pig]
* [Использование Hive с HDInsight][hive]
* [Отправка заданий Hadoop программными средствами][submit-jobs-programmatically]
* [Документация по пакету Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Отладка HDInsight: сообщения об ошибках][hdinsight-debug-error-messages]

[Powershell-install-configure]: /ru-ru/documentation/articles/install-configure-powershell/

[hdinsight-debug-error-messages]: /ru-ru/manage/services/hdinsight/debug-error-messages/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx
[getting-started]: /ru-ru/manage/services/hdinsight/get-started-hdinsight/

[hive]: /ru-ru/manage/services/hdinsight/using-hive-with-hdinsight/
[pig]: /ru-ru/manage/services/hdinsight/using-pig-with-hdinsight/
[pi-estimator]: /ru-ru/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/
[10gb-graysort]: /ru-ru/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/
[wordcount]: /ru-ru/manage/services/hdinsight/howto-run-samples/sample-wordcount/
[cs-streaming]: /ru-ru/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/

[submit-jobs-programmatically]: /ru-ru/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-resources]: /ru-ru/manage/services/hdinsight/introduction-hdinsight/


