<properties 
   pageTitle="Приступая к работе с Hadoop с Hive в HDInsight на платформе Linux| Azure" 
   description="Приступая к работе с Hadoop в HDInsight на платформе Linux. Информация о подготовке кластеров HDInsight Hadoop на платформе Linux и запрос данных с помощью Hive" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# Приступая к работе с Hadoop с Hive в HDInsight на платформе Linux (предварительная версия)

Этот учебник поможет приступить к работе с HDInsight в ОС Linux. В нем рассказывается о том, как подготовить кластер HDInsight Hadoop в Linux и отправить запрос Hive для получения полезных сведений из неструктурированных данных Hive. Затем результаты нужно будет проанализировать в средстве бизнес-аналитики, например Tableau.


> [AZURE.NOTE] Если вы не знакомы с Hadoop и программами обработки данных большого размера, ознакомьтесь с такими терминами, как <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a>и <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Чтобы разобраться, как HDInsight поддерживает Hadoop в Azure, см. раздел [Введение для Hadoop в HDInsight](../hdinsight-hadoop-introduction/).


## Цели учебника ##

Предположим, что у вас есть большой набор неструктурированных данных, и необходимо выполнить запросы для извлечения из него содержательной информации. Вот как это делается:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**Предварительные требования:**

Перед началом работы с этим учебником необходимо иметь следующее:


- Подписка Azure. Дополнительную информацию о получении подписки в разделе <a href="http://azure.microsoft.com/ pricing/purchase-options/" target="_blank">"Варианты приобретения"</a>, <a href="http://azure.microsoft.com/ pricing/member-offers/" target="_blank">"Предложения для участников"</a>или <a href="http://azure.microsoft.com/ pricing/free-trial/" target="_blank">"Бесплатная пробная версия"</a>.

**Предполагаемое время для выполнения:** 30 минут

## В этом учебнике рассматриваются следующие темы:

* [Создание учетной записи хранения Azure](#storage)
* [Подготовка кластера HDInsight на платформе Linux](#provision)
* [Отправка задания Hive](#hivequery)
* [Дальнейшие действия](#nextsteps)

## <a name="storage"></a>Создание учетной записи хранения Azure

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Оно называется *WASB* или *Azure Storage - Blob*. WASB - это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительную информацию см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage/).

При подготовке кластера HDInsight указывается учетная запись хранения Azure Storage. Конкретный контейнер хранилища BLOB-объектов из этой учетной записи назначается в качестве файловой системы по умолчанию, так же как и в HDFS. По умолчанию подготовка кластера HDInsight выполняется в том же центре обработки данных, в котором находится указанная учетная запись хранения.

Помимо этой учетной записи хранения в процессе настройки кластера HDInsight можно добавить дополнительные учетные записи хранения. Эти дополнительные учетные записи хранения могут быть из той же подписки Azure или из других подписок Azure. Указания см. в разделе [Подготовка кластеров HDInsight на платформе Linux с использованием настраиваемых параметров](../hdinsight-hadoop-provision-linux-clusters). 

Чтобы упростить этот учебник, используются только контейнер BLOB-объектов и учетная запись хранения по умолчанию. На практике файлы данных обыкновенно хранятся в заданной учетной записи хранения.

**Создание учетной записи хранения Azure**

1. Осуществите вход на <a href="https://manage.windowsazure.com/" target="_blank">портал управления Azure</a>.
2. Щелкните **СОЗДАТЬ** в левом нижнем углу, выберите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, а затем щелкните **БЫСТРО СОЗДАТЬ**.

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. Введите сведения в поля **URL-АДРЕС**, **РАСПОЛОЖЕНИЕ** и **РЕПЛИКАЦИЯ**, а затем щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**. Территориальные группы не поддерживаются. Вы увидите новую учетную запись хранения в списке хранилищ.

	>[AZURE.NOTE]  Если для подготовки кластера HDInsight на платформе Linux используется параметр "Быстрое создание", как в этом учебнике, то расположение при подготовке кластера не указывается. По умолчанию кластер располагается в том же центре обработки данных, что и учетная запись хранения. Таким образом, необходимо создать учетную запись хранения в тех местах, в которых поддерживается кластер, а именно:  **Восточная Азия**, **Юго-Восточная Азия**, **Северная Европа**, **Западная Европа**, **Восток США**, **Запад США**, **Северо-центральный регион США**, **Южно-центральный регион США**.

4. Дождитесь, пока **СОСТОЯНИЕ** новой учетной записи хранения не изменится на **В сети**.
5. Выберите новую учетную запись хранения в списке и щелкните **УПР. КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
7. Запишите значение параметров **ИМЯ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** и **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА** (или **ВТОРИЧНЫЙ КЛЮЧ ДОСТУПА**. Любой из ключей будет работать).  Они потребуются позже в данном руководстве.


Дополнительную информацию см. в разделах
[Создание учетной записи хранения](../storage-create-storage-account/) и [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage/).
	
## <a name="provision"></a>Подготовка кластера HDInsight на платформе Linux

При подготовке кластера HDInsight выполняется подготовка вычислительных ресурсов Azure, которые содержат Hadoop и соответствующие приложения. В этом разделе рассказывается о подготовке кластера HDInsight на платформе Linux с использованием параметра "Быстрое создание". При выборе этого параметра используются имена пользователей по умолчанию и контейнеры хранилища Azure, а также настраивается кластер HDInsight версии 3.2 (Hadoop версии 2.5, HDP версии 2.2) на платформе Ubuntu 12.04 LTS. Для получения дополнительной информации о различных версиях HDInsight и их соглашениях SLA см. страницу [Версии компонентов НDInsight](http://azure.microsoft.com/ documentation/articles/hdinsight-component-versioning/).

>[AZURE.NOTE]  Можно также создать кластеры Hadoop на платформе ОС Windows Server. Указания см. в статье [Приступая к работе с HDInsight в Windows](../hdinsight-get-started/).


**Вот как можно подготовить кластер HDInsight** 

1. Осуществите вход на <a href="https://manage.windowsazure.com/" target="_blank">портал управления Azure</a>. 

2. Последовательно выберите **СОЗДАТЬ** (в нижнем левом углу), **Службы данных**, **HDInsight** и **Hadoop в Linux**.

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. Введите или выберите следующие значения:

	<table border="1">
	<tr><th>Имя</th><th>Значение</th></tr>
	<tr><td>Имя кластера,</td><td>Имя кластера</td></tr>
	<tr><td>Размер кластера</td><td>Число узлов данных, которые требуется развернуть. Значение по умолчанию - 4. Но кластеры с 1 или 2 узлами данных также доступны в раскрывающемся списке. Можно указать любое количество узлов кластера <strong>с помощью параметра "Настраиваемое создание"</strong> . Доступны сведения о стоимости использования для различных размеров кластеров. Щелкните символ <strong>?</strong> над раскрывающимся списком и перейдите по ссылке во всплывающей подсказке.</td></tr>
	<tr><td>Пароль</td><td>Пароль для <i>HTTP</i> (учетная запись, по умолчанию имя пользователя: admin) и <i>SSH</i> (учетная запись, по умолчанию имя пользователя: hdiuser). Обратите внимание, что это НЕ учетные записи администратора для ВМ, на которых происходит подготовка кластеров. </td></tr>
	
	<tr><td>Учетная запись хранения</td><td>Выберите созданную вами учетную запись хранения в раскрывающемся списке. <br/>

	После выбора учетной записи хранения изменить ее невозможно. Если удалить учетную запись хранения, кластер будет недоступен для использования.

	Кластер HDInsight размещается в том же центре обработки данных, что и учетная запись хранения. 
	</td></tr>
	</table>

	Сохраните копию имени кластер. Они потребуются позже в данном руководстве.

	
5. Щелкните **Создать кластер HDInsight**. После завершения процесса подготовки в столбце состояния будет отображаться значение **Работает**.

	>[AZURE.NOTE] В ходе описанной выше процедуры создается кластер Linux с использованием параметра "Быстрое создание", который использует имя пользователя SSH по умолчанию и контейнеры хранилища Azure. Информацию о создании кластера с настраиваемыми параметрами, например с использованием ключа SSH для аутентификации или дополнительных учетных записей хранения, см. в статье [Подготовка кластеров HDInsight на платформе Linux с использованием настраиваемых параметров](../hdinsight-hadoop-provision-linux-clusters).


## <a name="hivequery"></a>Отправка задания Hive в кластере
Теперь, когда у вас есть подготовленный кластер HDInsight на платформе Linux, на следующем шаге выполняется пример задания Hive для запроса примера данных (sample.log), которые поставляются вместе с кластерами HDInsight. Пример данных содержит информацию из журнала, в том числе данные трассировки, предупреждения, информацию, ошибки и т. д. Мы запрашиваем эти данные, чтобы получить все журналы ошибок с определенным уровнем серьезности. Для выполнения запроса Hive в кластере HDInsight на платформе Linux нужно сделать следующее:

- подключиться к кластеру Linux;
- Выполнение задания Hive



### Подключение к кластеру

К кластеру HDInsight в ОС Linux можно подключиться с компьютера на платформе Linux или Windows с использованием SSH.

**Подключение с компьютера на платформе Linux**

1. Откройте терминал и введите следующую команду:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Поскольку подготовка кластера выполняется с использованием функции быстрого создания, имя пользователя SSH по умолчанию - **hdiuser**. Поэтому нужно использовать следующую команду:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. При появлении запроса введите пароль, который использовался при подготовке кластера. После успешного подключения строка запроса изменится следующим образом:

		hdiuser@headnode-0:~$


**Подключение с компьютера на платформе Windows**

1. Скачайте **PuTTY** для клиентов Windows. Это средство доступно на странице <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

2. Откройте **PuTTY**. В разделе **Категория** щелкните **Сеанс**. На экране **Основные параметры вашего сеанса PuTTY** в поле **Имя узла (или IP-адрес)** введите адрес SSH своего сервера HDInsight. Адрес SSH - это имя кластера с добавленным в конце суффиксом **-ssh.azurehdinsight.net**. Например, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. Чтобы сохранить данные соединения для использования в будущем, в разделе **Сохраненные сеансы** введите имя для этого подключения и нажмите кнопку **Сохранить**. Соединение будет добавлено в список сохраненных сеансов.

4. Щелкните **Открыть** для подключения к кластеру. При запросе имени пользователя введите *hdiuser*. При запросе пароля введите пароль, указанный при подготовке кластера. После успешного подключения строка запроса изменится следующим образом:

		hdiuser@headnode-0:~$

### Чтобы выполнить задание Hive

После подключения к кластеру с использованием SSH воспользуйтесь следующими командами для выполнения запроса Hive.

1. Запустите интерфейс командной строки Hive, используя следующую команду в командной строке:

		hive

2. С помощью этого интерфейса введите следующие инструкции, чтобы создать таблицу с именем log4jLogs, используя пример данных, который уже доступен в кластере.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Данная операция выполняет следующие действия:

	- **DROP TABLE** - удаляет таблицу и файл данных, если таблица уже существует.
	- **CREATE EXTERNAL TABLE** - создает новую  'external' таблицу в Hive. Внешние таблицы хранят только описание самой таблицы в Hive, в то время как данные остаются в исходном расположении.
	- **ROW FORMAT** - указывает Hive, как следует форматировать данные. В данном случае поля всех журналов разделены пробелом.
	- **STORED AS TEXTFILE LOCATION** - указывает Hive место, где хранятся данные (например, в каталоге example/data), а также то, что они хранятся в виде текста.
	- **SELECT** - подсчитывает количество строк, у которых столбец t4 содержит значение [ERROR]. 

	>[WACOM.NOTE] Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных или другой операцией MapReduce, при этом нужно, чтобы запросы Hive использовали самые последние данные. Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только описание таблицы.

	Будет возвращен следующий результат.

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched: 
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	Обратите внимание, что выходные данные содержат **[ERROR] 3**, поскольку это значение содержится в трех строках.

3. Используйте следующие операторы, чтобы создать новую таблицу internal с именем **errorLogs**.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Данная операция выполняет следующие действия:

	- **CREATE TABLE IF NOT EXISTS** - будет создана таблица, если она до этого не существовала. Так как ключевое слово EXTERNAL не было использовано, это таблица 'internal', то есть она будет храниться в хранилище данных Hive и полностью обслуживаться Hive. В отличие от **ВНЕШНИХ**, удаление внутренних таблиц приводит также к удалению данных.
	- **STORED AS ORC** - данные будут храниться в формате Optimized Row Columnar (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.
	- **INSERT OVERWRITE ... SELECT** - из таблицы **log4jLogs** будут выбраны строки, которые содержат значение [ERROR], а затем данные будут вставлены в таблицу **errorLogs**

4. Чтобы убедиться, что только строки, столбец t4 которых содержит значение **[ERROR]**, сохранены в таблице **errorLogs**, воспользуйтесь следующим запросом, чтобы отобразить все строки из таблицы errorLogs.

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	Возвращаемые данные должны соответствовать журналам [ошибок].


## <a name="nextsteps"></a>Дальнейшие действия
В этом учебнике вы узнали, как подготовить кластер Hadoop на платформе Linux с HDInsight и отправлять запросы Hive с использованием SSH. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Подготовка HDInsight на платформе Linux с использованием настраиваемых параметров](../hdinsight-hadoop-provision-linux-clusters)
- [Работа с HDInsight в Linux](../hdinsight-hadoop-linux-information)
- [Управление кластерами HDInsight с помощью Ambari](../hdinsight-hadoop-manage-ambari)
- [Использование MapReduce с HDInsight][hdinsight-use-mapreduce]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage)
- [Отправка данных в HDInsight][hdinsight-upload-data]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=45--> 
