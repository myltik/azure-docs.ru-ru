<properties
   	pageTitle="Учебник для Linux: начало работы с Hadoop и Hive | Microsoft Azure"
   	description="Следуйте инструкциям этого учебника для Linux, чтобы начать работу с Hadoop в HDInsight. Узнайте, как подготовить кластеры в Linux и запросить данные с помощью Hive."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="10/23/2015"
   	ms.author="nitinme"/>

# Учебник по Hadoop: приступая к работе с Hadoop с Hive в HDInsight на платформе Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Этот документ поможет вам быстро приступить к работе с Azure HDInsight в Linux. В нем рассказывается о том, как создать кластер Hadoop под управлением Linux, подключиться к кластеру в помощью SSH и выполнить запрос Hive с использованием включенных в кластер примеров данных.

> [AZURE.NOTE]Если вы не знакомы с Hadoop и программами больших данных, ознакомьтесь с такими терминами, как [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [распределенная файловая система Hadoop (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) и [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Чтобы разобраться, как HDInsight поддерживает Hadoop в Azure, см. [Введение для Hadoop в HDInsight](hdinsight-hadoop-introduction.md).

## Предварительные требования

Перед началом работы с этим учебником по Hadoop на базе Linux необходимо иметь следующее:

- **Подписку на Azure**: См. [Получение бесплатной пробной версии Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Клиент SSH** — в системах Linux, Unix и OS X клиент SSH предоставляется с помощью команды `ssh`. Для систем Windows рекомендуем использовать [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    > [AZURE.NOTE]В действиях, описанных в этом документе, для подключения к кластеру HDInsight используется SSH, так как SSH подходит для всех клиентских операционных систем. Другие способы подключения к кластеру HDInsight, такие как использование средств HDInsight для Visual Studio или API REST см. по ссылкам Hive, Pig и MapReduce в разделе [Дальнейшие действия](#nextsteps) данного документа.
    
- **Ключи SSH** (необязательно): для защиты учетной записи SSH, которая используется для подключения к кластеру с использованием пароля или открытого ключа. Использование пароля позволяет быстро начать работу, и этот вариант следует использовать, если вы хотите быстро подготовить кластер и запустить некоторые тестовые задания. Использование ключа более безопасно, но требует дополнительной настройки. Этот вариант можно использовать при подготовке рабочего кластера. В этой статье мы используем вариант с паролем. Инструкции по созданию и использованию ключей SSH в HDInsight см. в следующих статьях:

	-  на компьютере под управлением Linux — [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md);
    
	-  на компьютере под управлением Windows — [Использование SSH с кластерами HDInsight (Hadoop) под управлением Linux в Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a name="provision"></a>Подготовка кластера HDInsight на платформе Linux

При подготовке кластера создаются вычислительные ресурсы Azure, которые содержат службы и ресурсы Hadoop. В этом разделе подготавливается кластер HDInsight версии 3.2, содержащий Hadoop версии 2.2. Дополнительную информацию о различных версиях HDInsight и их соглашениях об уровне обслуживания см. в статье [Версии компонентов НDInsight](hdinsight-component-versioning.md). Дополнительные сведения о создании кластера HDInsight см. в статье [Подготовка кластеров HDInsight с использованием настраиваемых параметров][hdinsight-provision].

>[AZURE.NOTE]Вы также можете создать кластеры Hadoop под управлением операционной системы Windows Server. Инструкции см. в статье [Начало работы с HDInsight в Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

Чтобы создать новый кластер, выполните следующие действия.

1. Выполните вход на [портал предварительной версии Azure](https://ms.portal.azure.com/).
2. Последовательно выберите **СОЗДАТЬ**, **Анализ данных**, а затем — **HDInsight**.

    ![Создание нового кластера на портале предварительной версии Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Создание нового кластера на портале предварительной версии Azure")

3. Введите **Имя кластера**, выберите **Hadoop** в качестве **Типа кластера** и в раскрывающемся списке **Операционная система кластера** выберите **Ubuntu**. Если имя кластера доступно, рядом с ним появится зеленый флажок.

	![Введите имя и тип кластера](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Введите имя и тип кластера")

4. Если у вас есть несколько подписок, выберите запись **Подписка**, чтобы выбрать подписку Azure для кластера.

5. Щелкните **Группа ресурсов**, чтобы просмотреть список существующих групп ресурсов и выбрать ту, в которой будет создан кластер. Или выберите **Создать** и введите имя новой группы ресурсов. Если новое имя группы доступно, рядом с ним появится зеленый флажок.

	> [AZURE.NOTE]Эта запись будет выбрана по умолчанию для одной из существующих групп ресурсов (при их наличии).

6. Щелкните **Учетные данные** и введите пароль для пользователя с правами администратора. Также необходимо ввести **Имя пользователя SSH**. В разделе **Тип проверки подлинности SSH** щелкните **Пароль** и укажите пароль для пользователя SSH. Нажмите кнопку **Выбрать** внизу страницы, чтобы сохранить учетные данные конфигурации.

	![Укажите учетные данные кластера](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Укажите учетные данные кластера")

    > [AZURE.NOTE]SSH используется для удаленного доступа к кластеру HDInsight с помощью командной строки. Эти имя пользователя и пароль будут использоваться при подключении к кластеру через SSH. Имя пользователя SSH должно быть уникальным, так как на его основе создается учетная запись пользователя на всех узлах кластера HDInsight. Вот некоторые имена учетных записей, которые зарезервированы для служб в кластере и не должны использоваться в качестве имени пользователя SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Щелкните **Источник данных**, чтобы выбрать для кластера существующий источник данных или создать новый. При подготовке кластера Hadoop в HDInsight нужно указать учетную запись хранения Azure. Конкретный контейнер хранилища BLOB-объектов из этой учетной записи назначается в качестве файловой системы по умолчанию, так же как и в распределенной файловой системе Hadoop (HDFS). По умолчанию подготовка кластера HDInsight выполняется в том же центре обработки данных, в котором находится указанная учетная запись хранения. Дополнительные сведения см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight](hdinsight-use-blob-storage.md)

	![Колонка "Источник данных"](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Укажите конфигурацию источника данных")

	В настоящее время в качестве источника данных для кластера HDInsight можно выбрать учетную запись хранения Azure. Далее приводится описание элементов интерфейса, которое поможет вам лучше понять записи в колонке **Источник данных**.

	- **Метод выбора** — выберите значение **Из всех подписок**, чтобы активировать поиск учетных записей хранения во всех своих подписках. Задайте для этого параметра значение **Ключ доступа**, если вы хотите ввести **имя хранилища** и **ключ доступа** существующей учетной записи хранения.

	- **Выбрать учетную запись хранения/создать новую** — щелкните **Выбрать учетную запись хранения**, чтобы найти и выбрать существующую учетную запись хранения, которую нужно связать с кластером. Чтобы создать новую учетную запись хранения, выберите **Создать**. В появившееся поле введите имя учетной записи хранения. Если имя доступно, появится зеленый флажок.

	- **Выбрать контейнер по умолчанию**. Эта команда позволяет ввести имя контейнера по умолчанию и использовать его для кластера. Вы можете ввести любое имя, однако мы рекомендуем использовать такое же имя, как у кластера, чтобы легко распознавать, какой контейнер используется для конкретного кластера.

	- **Расположение** — географический регион, к которому будет относиться существующая или новая учетная запись хранения.

		> [AZURE.IMPORTANT]Выбранное расположение для источника данных по умолчанию будет также определять расположение кластера HDInsight. Кластер и источник данных по умолчанию должны находиться в одном регионе.

	Нажмите **Выбрать**, чтобы сохранить конфигурацию источника данных.

8. Щелкните **Ценовые категории узла**, чтобы отобразить сведения об узлах, которые будут созданы для этого кластера. Задайте количество рабочих узлов, необходимых для кластера. Оценочная стоимость кластера отобразится в колонке.

	![Колонка "Ценовые категории узла"](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Укажите количество узлов кластера")
    
    > [AZURE.IMPORTANT]Если вы планируете использовать более 32 рабочих узлов (при создании кластера или в ходе масштабирования после создания кластера), для головного узла потребуется минимум 8-ядерный процессор и 14 ГБ ОЗУ.
    >
    > Дополнительные сведения о размерах узлов и связанных затратах см. в статье [Сведения о ценах на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Нажмите **Выбрать**, чтобы сохранить конфигурацию цен узла.

9. В колонке **Новый кластер HDInsight** обязательно выберите параметр **Закрепить на начальной панели**, а затем нажмите кнопку **Создать**. После этого кластер будет создан, а на начальную панель портала Azure будет добавлена его плитка. Значок указывает, что выполняется подготовка кластера. После завершения подготовки вместо него будет отображаться значок HDInsight.

Подготовка выполняется|Подготовка завершена
------------------|---------------------
	![Индикатор подготовки на начальной панели](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Плитка подготовки кластера](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]Обычно создание кластера занимает около 15 минут. С помощью элемента на начальной панели или записи **Уведомления** в левой части страницы можно отслеживать процесс подготовки к работе.

После завершения подготовки щелкните плитку кластера на начальной панели, чтобы открыть колонку кластера.

## <a name="connect"></a> Подключение к кластеру

К кластеру HDInsight в Linux можно подключиться с компьютера под управлением Linux или Windows с использованием SSH.

###Подключение с компьютера на платформе Linux

1. Откройте терминал и введите следующую команду:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Так как подготовка кластера выполняется с использованием параметра «Быстрое создание», имя пользователя SSH по умолчанию — **hdiuser**. Поэтому нужно использовать следующую команду:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. При появлении запроса введите пароль, который использовался при подготовке кластера. После успешного подключения строка запроса изменится следующим образом:

		hdiuser@headnode-0:~$


###Подключение компьютера под управлением Windows

1. Загрузите [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) для клиентов Windows.

2. Откройте PuTTY. В разделе **Категория** щелкните **Сеанс**. На экране **Основные параметры вашего сеанса PuTTY** в поле **Имя узла (или IP-адрес)** введите адрес SSH своего сервера HDInsight. Адрес SSH — это имя кластера с добавленным в конце суффиксом **-ssh.azurehdinsight.net**. Например, **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Подключитесь к кластеру HDInsight на платформе Linux с помощью PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Чтобы сохранить данные о подключении для использования в будущем, в разделе **Сохраненные сеансы** введите имя для этого подключения и нажмите кнопку **Сохранить**. Соединение будет добавлено в список сохраненных сеансов.

4. Щелкните **Открыть** для подключения к кластеру. При запросе имени пользователя введите **hdiuser**. При запросе пароля введите пароль, указанный при подготовке кластера. После успешного подключения строка запроса изменится следующим образом:

		hdiuser@headnode-0:~$

##<a name="hivequery"></a>Выполнение запроса Hive

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

Из этого документа вы узнали, как создать кластер HDInsight под управлением Linux с использованием портала предварительной версии Azure, подключиться к кластеру с помощью SSH и выполнять основные запросы Hive.

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях:

- Дополнительные сведения об использовании Hive с HDInsight, включая выполнение запросов Hive из Visual Studio, см. в статье [Использование Hive с HDInsight][hdinsight-use-hive].

- Дополнительные сведения о Pig, языке, который используется для преобразования данных, см. в статье [Использование Pig с HDInsight][hdinsight-use-pig].

- Дополнительные сведения о MapReduce, способе записи программ, которые обрабатывают данные в Hadoop, см. в статье [Использование MapReduce с HDInsight][hdinsight-use-mapreduce].

- Дополнительные сведения об анализе данных в HDInsight с помощью средств HDInsight для Visual Studio см. в статье [Начало работы со средствами Hadoop Visual Studio для HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Если вы готовы приступить к работе с собственными данными и хотите узнать больше о том, как HDInsight сохраняет данные или как получать данные в HDInsight, см. следующие статьи:

- Информацию о том, как HDInsight использует хранилище больших двоичных объектов Azure, см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-use-blob-storage.md).

- Сведения об отправке данных в HDInsight см. в статье [Отправка данных в HDInsight][hdinsight-upload-data].

Дополнительные сведения о создании кластера HDInsight и управлении этим кластером см. в следующих статьях:

- Информацию об управлении кластером HDInsight под управлением Linux см. в статье [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md).

- Дополнительные сведения о параметрах, которые можно выбрать при создании кластера HDInsight, см. в статье [Подготовка HDInsight в Linux с использованием настраиваемых параметров](hdinsight-hadoop-provision-linux-clusters.md).

- Если вы уже знакомы с Linux и Hadoop, но хотите узнать об особенностях Hadoop в HDInsight, см. статью [Работа с HDInsight в Linux](hdinsight-hadoop-linux-information.md). Она содержит следующую информацию:

	* URL-адреса для служб, размещенных в кластере, например Ambari и WebHCat;
	* расположение файлов Hadoop и примеры в локальной файловой системе;
	* использование хранилища Azure (WASB) вместо HDFS в качестве хранилища данных по умолчанию.


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

<!---HONumber=Nov15_HO1-->