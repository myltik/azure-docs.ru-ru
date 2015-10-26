<properties
	pageTitle="Выполнение учебных задач Hadoop в Azure HDInsight | Microsoft Azure"
	description="Вы начнете использовать службу Azure HDInsight с помощью представленных примеров. Использование сценариев PowerShell, которые запускают программы MapReduce в кластерах данных."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>




#Выполнение примеров Hadoop в HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Предоставляется ряд примеров, которые помогут начать работу с заданиями MapReduce на кластерах Hadoop с использованием Azure HDInsight. Эти примеры доступны на каждом создаваемом управляемом кластере HDInsight. Выполнение примеров позволит вам научиться использовать командлеты Azure PowerShell для запуска заданий в кластерах Hadoop.

Программы MapReduce также можно запускать программно из приложения, используя API Microsoft .NET для HDInsight. Дополнительные сведения об отправке заданий с помощью интерфейсов API для HDInsight см. в статье [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs].

В Интернете есть много дополнительной документации по связанным с Hadoop технологиям, например по программированию и потоковой передаче MapReduce с использованием Java, а также документация по командлетам, используемым в сценариях PowerShell. Дополнительные сведения об этих ресурсах см. в статье [Основные сведения об Azure HDInsight][hdinsight-introduction] в итоговом разделе **Ресурсы для HDInsight**.

**Что представляют собой эти примеры**

<p>Эти примеры позволят вам быстро ознакомиться с развертыванием заданий Hadoop, так как представляют собой своего рода испытательный стенд для концепций и сценариев процедур, которые используются в службе. В этих примера мы предлагаем вам рассмотреть некоторые типичные задачи, в частности создание и импорт наборов данных различных размеров, выполнение заданий, их последовательное составление, а также просмотр результатов их выполнения заданий. Используемые наборы данных могут иметь разный размер. Это позволяет вам увидеть, как размер набора влияет на скорость выполнения заданий.</p>


**Предварительные требования**:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Кластер HDInsight**. Сведения по различным способам создания таких кластеров см. в разделе [Подготовка кластеров HDInsight](hdinsight-provision-clusters.md)
- **Рабочая станция с Azure PowerShell**. См. [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## Примеры ##

HDInsight поставляется вместе со следующими примерами.

- [**Пример оценки числа пи с помощью Hadoop**][hdinsight-sample-pi-estimator]. Этот пример демонстрирует, как запустить программу MapReduce в службе HDInsight, в которой используется статистический метод (квази-Монте-Карло) для оценки числа пи.
- [**Пример счетчика слов в кластере Hadoop**][hdinsight-sample-wordcount]. Этот пример демонстрирует, как с помощью кластера HDInsight запустить программу MapReduce, которая подсчитывает количество вхождений слова в текстовом файле.
- [**Пример сортировки данных объемом 10 ГБ (Graysort)**][hdinsight-sample-10gb-graysort] Этот пример демонстрирует, как выполнить универсальную сортировку GraySort для файла размером 10 ГБ, используя службу HDInsight. Выполняются три задания: Teragen создает данные, Terasort сортирует эти данных и Teravalidate проверяет правильность их сортировки.
- [**Пример потокового счетчика слов MapReduce на C#**][hdinsight-sample-csharp-streaming]. В этом примере показано, как на C# написать программу MapReduce, в которой используется интерфейс потоковой передачи Hadoop.


## Как выполнять примеры ##

Примеры можно выполнить с помощью Azure PowerShell. Сведения о том, как это делать, приведены на страницах с указанными выше примерами.

##Дальнейшие действия ##

Из этой статьи, а также из статей, сопровождающих каждый пример, вы узнали, как с помощью среды Azure PowerShell выполнять примеры, включенные в кластеры HDInsight. Учебники по использованию Pig, Hive и MapReduce в службе HDInsight см. в следующих статьях.

* [Приступая к работе с Hadoop с использованием Hive в HDInsight для анализа данных об использовании мобильного телефона][hdinsight-get-started]
* [Использование Pig с Hadoop в HDInsight][hdinsight-use-pig]
* [Использование Hive с Hadoop в HDInsight][hdinsight-use-hive]
* [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs]
* [Документация по пакету SDK для Azure HDInsight][hdinsight-sdk-documentation]
* [Отладка Hadoop в HDInsight: сообщения об ошибках][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=Oct15_HO3-->