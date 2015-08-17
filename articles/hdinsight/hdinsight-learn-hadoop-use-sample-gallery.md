<properties
   pageTitle="Изучение Hadoop в HDInsight с помощью коллекции примеров | Microsoft Azure"
   description="С помощью коллекции для начала работы с HDInsight вы можете быстро изучить Hadoop, запуская примеры приложений. Можно использовать как предоставленный пример данных или как собственный."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.date="07/09/2015"
   ms.author="jgao"/>

# Изучение Hadoop с помощью коллекции для начала работы с HDInsight

С помощью коллекции для начала работы с HDInsight можно легко и быстро изучить Hadoop, запуская примеры приложений в HDInsight. Некоторые примеры поставляются с примером данных. Для остальных примеров можно указать собственные данные. Сейчас доступно шесть следующих примеров (в будущем их будет больше):

- Решения с данными Azure
	- Анализ журналов веб-сайта Microsoft Azure
	- Аналитика службы хранилища Microsoft Azure
- Решения с примером данных
	- Анализ данных датчика
	- Анализ тенденций Twitter
	- Анализ журнала веб-сайта
	- Рекомендации по фильмам с использованием Mahout

![Решения из коллекции для начала работы с HDInsight Hadoop, Storm и HBase, включая примеры данных.][hdinsight.sample.gallery]

В следующем видеоролике показано, как запустить пример анализа тенденций Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>


**Запуск примера из коллекции для начала работы**

1.	Войдите на [портал Azure][azure.portal].
2.	В меню слева щелкните **HDInsight**. Появится список существующих кластеров HDInsight, в том числе Hadoop, Storm и HBase.
3.	Щелкните кластер, в котором вы хотите запустить пример.
4.	В нижней части страницы щелкните **КОНСОЛЬ ЗАПРОСОВ**.
5.	Введите имя пользователя и пароль Hadoop для кластера.
6.	В верхней части страницы щелкните **Коллекция для начала работы**.
7.	Выберите один из примеров. К каждому примеру прилагаются подробные указания по его запуску. На следующем рисунке приведен пример анализа тенденций Twitter:

	![Пример анализа тенденций Twitter в HDInsight][hdinsight.twitter.sample]

## Дальнейшие действия
HDInsight можно также изучить другими способами:

- [Карта обучения HDInsight][hdinsight.learn.map]
- [Инфографика по HDInsight][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: hdinsight-learn-map.md
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://manage.windowsazure.com
 

<!---HONumber=August15_HO6-->