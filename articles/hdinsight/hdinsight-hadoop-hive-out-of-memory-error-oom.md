.<properties
	pageTitle="Ошибка нехватки памяти (OOM) — параметры Hive | Microsoft Azure"
	description="Устранение ошибки нехватки памяти (OOM) в запросе Hive в HDInsight. Пользовательский сценарий представляет собой запрос ко множеству больших таблиц."
	keywords="ошибка нехватки памяти, OOM, параметры Hive"
	services="hdinsight"
	documentationCenter=""
	authors="rashimg"
	manager="paulettm"
	editor="cgronlun"/>

.<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="05/18/2016"
	ms.author="rashimg;jgao"/>

# Устранение ошибки нехватки памяти (OOM) с помощью параметров памяти Hive в Hadoop в Azure HDInsight

Одной из распространенных проблем, с которой сталкиваются наши клиенты, является ошибка нехватки памяти (OOM) при работе с Hive. В этой статье описывается сценарий клиента и параметры Hive, которые рекомендуется использовать для решения проблемы.

## Сценарий. Запрос Hive для больших таблиц

Клиент выполнил приведенный ниже запрос с помощью Hive.

	SELECT
		COUNT (T1.COLUMN1) as DisplayColumn1,
		…
		…
		….
	FROM
		TABLE1 T1,
		TABLE2 T2,
		TABLE3 T3,
		TABLE5 T4,
		TABLE6 T5,
		TABLE7 T6
	where (T1.KEY1 = T2.KEY1….
		…
		…

Некоторые особенности этого запроса:

* T1 является псевдонимом для большой таблицы TABLE1, которая содержит множество столбцов типа STRING.
* Другие таблицы не так велики, но имеют большое число столбцов.
* Все таблицы соединяются друг с другом, в некоторых случаях с несколькими столбцами в TABLE1 и др.

При запуске запроса с помощью Hive на MapReduce на узле 24 кластера A3 на выполнение запроса потребовалось около 26 минут. При выполнении запроса с помощью Hive на MapReduce клиент заметил следующие предупреждения:

	Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
	Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Поскольку на выполнение запроса ушло примерно 26 минут, клиент проигнорировал эти предупреждения и сосредоточил свое внимание на том, как улучшить производительность запроса.

Клиент обратился к статье [Оптимизация запросов Hive для Hadoop в HDInsight](hdinsight-hadoop-optimize-hive-query.md) и решил использовать модуль выполнения Tez. Выполнение того же запроса с параметром Tez заняло 15 минут, после чего появилась следующая ошибка:

	Status: Failed
	Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
	org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
	org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
	Caused by: java.lang.OutOfMemoryError: Java heap space

Затем клиент решил использовать виртуальную машину большего размера (т. е. D12), полагая, что виртуальная машина большего размера будет иметь больший размер кучи. Даже в таком случае сообщение об ошибке продолжает появляться. Клиент обратился к команде HDInsight за помощью в отладке этой проблемы.

## Отладка ошибки нехватки памяти (OOM)

Наша инженерная команда и команда поддержки обнаружили, что одна из проблем, которые приводят к ошибке нехватки памяти (OOM), представляет собой [известную проблему, описанную в Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306). Из описания в JIRA:

	When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Заглянув в файл hive-site.xml, мы убедились, что свойство **hive.auto.convert.join.noconditionaltask** действительно было установлено в **true**:

	<property>
    	<name>hive.auto.convert.join.noconditionaltask</name>
    	<value>true</value>
    	<description>
      		Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
      		If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
      		specified size, the join is directly converted to a mapjoin (there is no conditional task).
    	</description>
  	</property>

На основе предупреждения и JIRA мы предположили, что источником ошибки нехватки памяти в пространстве кучи Java был Map Join. Поэтому мы проанализировали эту проблему глубже.

Как описано в записи блога [Параметры памяти Hadoop Yarn в HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), при использовании модуля Tez используемое пространство кучи на самом деле принадлежит контейнеру Tez. Описание памяти контейнера Tez см. на рисунке ниже.

![Схема памяти контейнера Tez: ошибка нехватки памяти Hive (OOM)](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Как следует из записи блога, два следующих параметра памяти определяют контейнер памяти для кучи: **hive.tez.container.size** и **hive.tez.java.opts**. Согласно нашему опыту, исключение нехватки памяти не означает, что размер контейнера слишком мал. Оно означает, что размер кучи Java (hive.tez.java.opts) слишком мал. Поэтому каждый раз, когда вы видите ошибку нехватки памяти, можно попытаться увеличить **hive.tez.java.opts**. При необходимости может потребоваться увеличение параметра **hive.tez.container.size**. Параметр **Java.opts** должен составлять около 80 % от **container.size**.

> [AZURE.NOTE]  Параметр **hive.tez.java.opts** всегда должен быть меньше, чем **hive.tez.container.size**.

Так как виртуальная машина размера D12 имеет 28 ГБ памяти, мы решили использовать контейнер размером 10 ГБ (10240 МБ) и назначить 80 % от этого размера параметру java.opts. Это было сделано в консоли Hive с использованием следующего параметра:

	SET hive.tez.container.size=10240
	SET hive.tez.java.opts=-Xmx8192m

С этими настройками запрос был выполнен успешно, и его выполнение заняло не более 10 минут.

## Заключение. Ошибки нехватки памяти и размер контейнера

Ошибка нехватки памяти не обязательно означает, что размер контейнера слишком мал. Вместо этого следует настроить параметры памяти, увеличив размер кучи, так чтобы он составлял не менее 80 % от размера памяти контейнера.

<!---HONumber=AcomDC_0817_2016-->