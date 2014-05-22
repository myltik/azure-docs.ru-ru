<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Монитор" pageTitle="Монитор HDInsight | Azure" metaKeywords="" description="Узнайте, как вести наблюдение за кластером HDInsight и просматривать журнал заданий Hadoop на портале управления Azure." metaCanonical="" services="hdinsight" documentationCenter="" title="Наблюдение за HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />




# Наблюдение за HDInsight

В этой статье вы узнаете, как вести наблюдение за кластером HDInsight.

##Оглавление

* [Практическое руководство. Наблюдение за кластером HDInsight](#monitorcluster)
* [Практическое руководство. Просмотр журнала заданий Hadoop](#jobhistory)

##<a id="monitorcluster"></a> Практическое руководство. Наблюдение за кластером HDInsight

Для отслеживания работоспособности кластера HDInsight и выполняемых в нем заданий Hadoop можно подключиться к панели мониторинга HDInsight и щелкнуть плитку "Монитор кластера".

![HDI.TileMonitorCluster][hdi-monitor-cluster-tile]

Страница монитора выглядит следующим образом:

![HDI.MonitorPage][hdi-monitor-page]


В правой части окна показано, что узел имен и средство отслеживания заданий работают, при этом 4 узла данных находятся в исправном состоянии и выполняются.

В левой части окна отображаются метрики "map-reduce" за последние 30 минут. Доступны интервалы мониторинга: 30 минут, 1 час, 3 часа, 12 часов, 1 день, 2 дня, 1 неделя и 2 недели.

##<a id="jobhistory"></a> Практическое руководство. Просмотр журнала заданий Hadoop
Чтобы просмотреть журнал заданий Hadoop, подключитесь к HDInsight панели мониторинга и щелкните плитку "Журнал заданий". 

![HDI.TileJobHistory][hdi-job-history-tile]

Эта плитка отображает количество выполненных заданий. Например, на предыдущем рисунке показано, что доступен журнал для 6 заданий.  Страница журнала заданий выглядит следующим образом:

![HDI.JobHistoryPage][hdi-job-history-page]


## См. также

* [Практическое руководство. Администрирование HDInsight](/ru-ru/manage/services/hdinsight/howto-administer-hdinsight/)
* [Практическое руководство. Программное развертывание кластера HDInsight](/ru-ru/manage/services/hdinsight/howto-deploy-cluster/)
* [Практическое руководство. Программное выполнение удаленных заданий в кластере HDInsight](/ru-ru/manage/services/hdinsight/howto-execute-jobs-programmatically/)
* [Учебный курс. Приступая к работе с Azure HDInsight](/ru-ru/manage/services/hdinsight/get-started-hdinsight/)

[hdi-monitor-cluster-tile]: ./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG
[hdi-monitor-page]: ./media/hdinsight-monitor/HDI.MonitorPage.PNG
[hdi-job-history-tile]: ./media/hdinsight-monitor/HDI.TileJobHistory.PNG
[hdi-job-history-page]: ./media/hdinsight-monitor/HDI.JobHistoryPage.PNG

