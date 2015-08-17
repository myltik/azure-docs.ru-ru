<properties
   pageTitle="Учебник для Linux: начало работы с Hadoop и Hive | Microsoft Azure"
   description="Следуйте инструкциям этого учебника для Linux, чтобы начать работу с Hadoop в HDInsight. Узнайте, как подготовить кластеры в Linux и запросить данные с помощью Hive."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/11/2015"
   ms.author="nitinme"/>

# Учебник по Hadoop: приступая к работе с Hadoop с Hive в HDInsight на платформе Linux (предварительная версия)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Этот учебник по Hadoop поможет вам быстро приступить к работе с Azure HDInsight в Linux. В нем рассказывается о том, как подготовить кластер Hadoop в Linux и отправить запрос Hive.


> [AZURE.NOTE]Если вы не знакомы с Hadoop и программами обработки данных большого размера, ознакомьтесь с такими терминами, как <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">распределенная файловая система Hadoop (HDFS)</a> и <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Чтобы разобраться, как HDInsight поддерживает Hadoop в Azure, см. [Введение для Hadoop в HDInsight](hdinsight-hadoop-introduction.md).


## Цели учебника 

Предположим, что у вас есть большой набор неструктурированных данных, и необходимо выполнить запросы для извлечения из него содержательной информации. Вот как это можно осуществить:

   ![Шаги учебника по Hadoop: создать учетную запись хранения; подготовить кластер Hadoop; запросить данные с помощью Hive.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## Предварительные требования

Перед началом работы с этим учебником по Hadoop на базе Linux необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

**Предполагаемое время выполнения:** 30 минут

## В этом учебнике рассматриваются следующие темы:

* [Создание учетной записи хранения Azure](#storage)
* [Подготовка кластера HDInsight на платформе Linux](#provision)
* [Отправка задания Hive в кластере](#hivequery)
* [Дальнейшие действия](#nextsteps)

## <a name="storage"></a>Создание учетной записи хранения Azure

HDInsight использует для хранения данных хранилище больших двоичных объектов Azure. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).

При подготовке кластера HDInsight указывается учетная запись хранения Azure Storage. Конкретный контейнер хранилища больших двоичных объектов из этой учетной записи назначается в качестве файловой системы по умолчанию, так же как и в HDFS. По умолчанию подготовка кластера HDInsight выполняется в том же центре обработки данных, в котором находится указанная учетная запись хранения.

Помимо этой учетной записи хранения, в процессе настройки кластера HDInsight можно добавить другие учетные записи хранения. Они могут быть из той же подписки Azure или из других подписок Azure. Указания см. в статье [Подготовка кластеров HDInsight на платформе Linux с использованием настраиваемых параметров](hdinsight-hadoop-provision-linux-clusters.md).

Чтобы упростить этот учебник, используются только контейнер больших двоичных объектов и учетная запись хранения по умолчанию. На практике файлы данных обычно хранятся в заданной учетной записи хранения.

**Создание учетной записи хранения Azure**

1. Войдите на <a href="https://manage.windowsazure.com/" target="_blank">портал Azure</a>.
2. Щелкните **СОЗДАТЬ** в левом нижнем углу, последовательно выберите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, а затем щелкните **БЫСТРОЕ СОЗДАНИЕ**.

	![Портал Azure, на котором можно использовать функцию быстрого создания для настройки новой учетной записи хранения.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.StorageAccount.QuickCreate.png)

3. Введите сведения в поля **URL-АДРЕС**, **РАСПОЛОЖЕНИЕ** и **РЕПЛИКАЦИЯ**, а затем щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**. Территориальные группы не поддерживаются. Вы увидите новую учетную запись хранения в списке хранилищ.

	>[AZURE.NOTE]Если для подготовки кластера HDInsight на платформе Linux используется параметр «Быстрое создание», как в этом учебнике, то расположение при подготовке кластера не указывается. По умолчанию кластер располагается в том же центре обработки данных, что и учетная запись хранения.

4. Дождитесь, пока значение параметра **СОСТОЯНИЕ** для новой учетной записи хранения не изменится на **В сети**.
5. Выберите новую учетную запись хранения в списке и щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
7. Запишите значения параметров **ИМЯ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** и **ПЕРВИЧНАЯ КЛАВИША ДОСТУПА** (или **ВТОРИЧНАЯ КЛАВИША ДОСТУПА**). (Будет работать любая из клавиш.) Они потребуются позже в данном руководстве.


Дополнительную информацию см. в разделе [Как создать учетную запись хранения](../storage-create-storage-account.md) и статье [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md).

## <a name="provision"></a>Подготовка кластера HDInsight на платформе Linux

При подготовке кластера HDInsight происходит подготовка вычислительных ресурсов Azure, содержащих Hadoop и соответствующие приложения. В этом разделе рассказывается о подготовке кластера HDInsight на платформе Linux с использованием параметра «Быстрое создание». С этим параметром используются имена пользователей по умолчанию и контейнеры хранилища Azure, а также настраивается кластер HDInsight версии 3.2 (Hadoop версии 2.6, Hortonworks Data Platform версии 2.2) на платформе Ubuntu 12.04 LTS с долгосрочной поддержкой). Дополнительную информацию о различных версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]Вы также можете создать кластеры Hadoop под управлением операционной системы Windows Server. Указания см. в статье о [начале работы в HDInsight](../hdinsight-get-started.md).


**Подготовка кластера HDInsight**

1. Войдите на <a href="https://manage.windowsazure.com/" target="_blank">портал Azure</a>.

2. Щелкните **СОЗДАТЬ** в нижнем левом углу, последовательно выберите **СЛУЖБЫ ДАННЫХ** и **HDINSIGHT**, а затем щелкните **HADOOP В LINUX**.

	![Создание кластера Hadoop в HDInsight.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.QuickCreateCluster.png)

4. Введите или выберите следующие значения:

	<table border="1">
<tr><th>Имя</th><th>Значение</th></tr>
<tr><td>Имя кластера,</td><td>Имя кластера.</td></tr>
<tr><td>Размер кластера</td><td>Число узлов данных, которые требуется развернуть. Значение по умолчанию&#160;— 4. Но кластеры с 1 или 2 узлами данных также доступны в раскрывающемся списке. С помощью параметра <strong>Настраиваемое создание</strong> может быть указано любое количество узлов кластера. Доступны сведения о стоимости использования для различных размеров кластеров. Щелкните символ <strong>?</strong> над раскрывающимся списком и перейдите по ссылке во всплывающем окне.</td></tr>
<tr><td>Пароль</td><td>Пароль для учетной записи <i>HTTP</i> (имя пользователя по умолчанию — admin) и учетная запись <i>SSH</i> (имя пользователя по умолчанию — hdiuser). Обратите внимание, что это НЕ учетные записи администратора для виртуальных машин, на которых происходит подготовка кластеров. </td></tr>

<tr><td>Учетная запись хранения</td><td>Выберите созданную вами учетную запись хранения в раскрывающемся списке. <br/>

Выбрав, ее уже невозможно изменить. Если удалить учетную запись хранения, кластер будет недоступен для использования. Кластер HDInsight размещается в том же центре обработки данных, что и учетная запись хранения.
</td></tr>
</table>Сохраните копию имени кластер. Они потребуются позже в данном руководстве.


5. Щелкните **СОЗДАТЬ КЛАСТЕР HDINSIGHT**. После завершения процесса подготовки в столбце состояния будет отображаться **Работает**.

	>[AZURE.NOTE]В ходе описанной выше процедуры создается кластер Linux с использованием параметра «Быстрое создание», который использует имя пользователя SSH по умолчанию и контейнеры службы хранилища Azure. Информацию о создании кластера с настраиваемыми параметрами, например с использованием ключа SSH для аутентификации или дополнительных учетных записей хранения, см. в статье [Подготовка кластеров HDInsight на платформе Linux с использованием настраиваемых параметров](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="hivequery"></a>Отправка задания Hive в кластере
Теперь, когда у вас есть подготовленный кластер HDInsight на платформе Linux, на следующем шаге выполняется пример задания Hive для запроса примера данных (sample.log), которые поставляются вместе с кластерами HDInsight. В примере данных содержится информация журнала, в том числе данные трассировки, предупреждения, ошибки и другая информация. Мы запрашиваем эти данные, чтобы получить все журналы ошибок с определенным уровнем серьезности. Для выполнения запроса Hive в кластере HDInsight на платформе Linux нужно сделать следующее:

- подключиться к кластеру Linux;
- Выполнение задания Hive



### Подключение к кластеру

К кластеру HDInsight в Linux можно подключиться с компьютера под управлением Linux или Windows с использованием SSH.

**Подключение с компьютера под управлением Linux**

1. Откройте терминал и введите следующую команду:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Так как подготовка кластера выполняется с использованием параметра «Быстрое создание», имя пользователя SSH по умолчанию — **hdiuser**. Поэтому нужно использовать следующую команду:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. При появлении запроса введите пароль, который использовался при подготовке кластера. После успешного подключения строка запроса изменится следующим образом:

		hdiuser@headnode-0:~$


**Подключение компьютера под управлением Windows**

1. Скачайте <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a> для клиентов Windows.

2. Откройте PuTTY. В разделе **Категория** щелкните **Сеанс**. На экране **Основные параметры вашего сеанса PuTTY** в поле **Имя узла (или IP-адрес)** введите адрес SSH своего сервера HDInsight. Адрес SSH — это имя кластера с добавленным в конце суффиксом **-ssh.azurehdinsight.net**. Например, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Подключитесь к кластеру HDInsight на платформе Linux с помощью PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Чтобы сохранить данные о подключении для использования в будущем, в разделе **Сохраненные сеансы** введите имя для этого подключения и нажмите кнопку **Сохранить**. Соединение будет добавлено в список сохраненных сеансов.

4. Щелкните **Открыть** для подключения к кластеру. При запросе имени пользователя введите **hdiuser**. При запросе пароля введите пароль, указанный при подготовке кластера. После успешного подключения строка запроса изменится следующим образом:

		hdiuser@headnode-0:~$

### Чтобы выполнить задание Hive

После подключения к кластеру через SSH воспользуйтесь следующими командами для выполнения запроса Hive.

1. Запустите интерфейс командной строки Hive, используя следующую команду в командной строке:

		hive

2. В интерфейсе командной строки введите следующие операторы, чтобы создать таблицу с именем **log4jLogs**, используя пример данных, который уже доступен в кластере.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Эти инструкции выполняют следующие действия.

	- **DROP TABLE** — удаляет таблицу и файл данных, если таблица уже существует.
	- **CREATE EXTERNAL TABLE** — создает новую внешнюю таблицу в Hive. Внешние таблицы хранят только описание самой таблицы в Hive, в то время как данные остаются в исходном расположении.
	- **ROW FORMAT** — указывает Hive, как следует форматировать данные. В данном случае поля всех журналов разделены пробелом.
	- **STORED AS TEXTFILE LOCATION**: информация для Hive о расположении хранения данных (каталог example/data) и об их формате (текстовый).
	- **SELECT**: подсчитывает количество строк, у которых столбец t4 содержит значение [ERROR].

	>[AZURE.NOTE]Внешние таблицы необходимо использовать в тех случаях, когда ожидается, что исходные данные будут обновляться внешним источником, таким как автоматизированный процесс передачи данных, или другой операцией MapReduce, и при этом нужно, чтобы запросы Hive использовали самые последние данные. Удаление внешней таблицы *не* приводит к удалению данных. Будет удалено только описание таблицы.

	В результате будут возвращены такие выходные данные:

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

3. Используйте следующие операторы, чтобы создать новую внутреннюю таблицу с именем **errorLogs**.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Эти инструкции выполняют следующие действия.

	- **CREATE TABLE IF NOT EXISTS**: создание таблицы, если она до этого не существовала. Так как ключевое слово **EXTERNAL** не было использовано, данная таблица будет внутренней. То есть она хранится в хранилище данных Hive, и ею полностью управляет Hive. В отличие от внешних таблиц, удаление внутренних таблиц приводит также к удалению данных.
	- **STORED AS ORC** — сохраняет данные в формате Optimized Row Columnar (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.
	- **INSERT OVERWRITE ... SELECT** — выбирает из таблицы **log4jLogs** строки, которые содержат значение [ERROR], а затем вставляет данные в таблицу **errorLogs**.

4. Чтобы убедиться в том, что в таблице **errorLogs** сохранены только строки, столбец t4 которых содержит значение [ERROR], воспользуйтесь следующим запросом, чтобы отобразить все строки из таблицы **errorLogs**.

		SELECT * from errorLogs;

	На консоли должны отобразиться следующие выходные данные:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	Возвращаемые данные должны соответствовать журналам ошибок [ERROR].


## <a name="nextsteps"></a>Дальнейшие действия
В этом учебнике для Linux вы узнали, как подготовить кластер Hadoop на платформе Linux с HDInsight и отправить запросы Hive с использованием SSH. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Подготовка HDInsight в Linux с использованием настраиваемых параметров](hdinsight-hadoop-provision-linux-clusters.md)
- [Работа с HDInsight в Linux](hdinsight-hadoop-linux-information.md)
- [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md)
- [Использование MapReduce с HDInsight][hdinsight-use-mapreduce]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Использование хранилища больших двоичных объектов Azure с HDInsight](../hdinsight-use-blob-storage.md)
- [Отправка данных в HDInsight][hdinsight-upload-data]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!---HONumber=August15_HO6-->