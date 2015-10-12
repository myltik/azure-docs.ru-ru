<properties
	pageTitle="Учебник по HBase: Приступая к работе с HBase на Hadoop | Microsoft Azure"
	description="Следуйте инструкциям этого учебника по HBase, чтобы начать работу с Apache HBase на Hadoop в HDInsight. Создание таблиц из оболочки HBase и обращение к ним с помощью Hive."
	keywords="apache hbase,hbase,hbase shell,hbase tutorial"
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
	ms.date="07/28/2015"
	ms.author="jgao"/>



# Учебник по HBase: Приступая к работе с Apache HBase на Hadoop в HDInsight

Узнайте, как настраивать кластер HBase на HDInsight, создавать таблицы HBase и запрашивать таблицы с помощью Hive. Для получения общих сведений о HBase обратитесь к разделу [Обзор HDInsight HBase][hdinsight-hbase-overview].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Учебник по HBase: Приступая к работе с Apache HBase на Hadoop в HDInsight](hdinsight-hbase-tutorial-get-started-v1.md)

> [AZURE.NOTE]Информация, приведенная в этом документе, относится только к кластерам HDInsight под управлением Windows. Сведения об использовании кластеров под управлением Linux можно найти в разделе [hdinsight-hbase-tutorial-get-started-linux.md).
>
> HBase (версия 0.98.0) на HDInsight под управлением Windows доступна только для использования с кластерами HDInsight версии 3.1 (на основе Apache Hadoop и YARN версии 2.4.0). Для получения информации о версиях ПО см. раздел [Новые возможности версий кластеров, предоставляемых HDInsight.][hdinsight-versions]

##Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Microsoft Azure**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Рабочая станция** с установленным ПО Visual Studio 2013: указания см. в разделе [Установка Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Подготовка кластера HBase

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Подготовка кластера HBase с помощью портала Azure**


1. Войдите на [портал Azure][azure-management-portal].
2. Щелкните **Создать** в верхнем левом углу и выберите **Данные + аналитика**, **HDInsight**.
3. Введите следующие значения.

	- **Имя кластера** — введите имя для идентификации этого кластера.
	- **Тип кластера** — HBase
	- **Операционная система кластера** — на данный момент кластер HBase HDInsight доступен только для операционной системы Windows.
	- **Подписка** — выберите подписку Azure, используемую для подготовки этого кластера.
	- **Группа ресурсов** — добавьте группу ресурсов Azure или выберите существующую. Дополнительную информацию см. в статье [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md).
	- **Настройка учетных данных** — для кластера на основе Windows можно создать пользователя кластера (также называемого HTTP-пользователем, пользователем веб-службы HTTP) и пользователя удаленного рабочего стола.
	- **Источник данных** — укажите существующую или создайте новую учетную запись хранения Azure, которая будет использована в качестве файловой системы по умолчанию для кластера. Эта учетная запись хранения Azure должна находиться в одном расположении с кластером HBase HDInsight.
	- **Укажите ценовые категории** — выберите количество серверов в регионе для кластера HBase.

		> [AZURE.WARNING]Для обеспечения высокой доступности служб HBase необходимо подготовить кластер, содержащий по крайней мере **три** узла. Это гарантирует, что если один узел выйдет из строя, области данных HBase будут доступны на других узлах.

		> Если вы изучаете HBase, всегда выбирайте 1 для размера кластера и удаляйте кластер после каждого использования для сокращения затрат.

	- **Необязательная конфигурация** — выберите версию кластера, настройте виртуальную сеть Azure, метахранилище Hive или Oozie, действия сценария и добавьте учетные записи хранения.

4. Щелкните **Создать**.

>[AZURE.NOTE]После удаления кластера HBase можно создать другой кластер HBase, использовав тот же контейнер BLOB-объектов по умолчанию. Таблицы HBase, созданные в исходном кластере, получит новый кластер.

## Использование оболочки HBase
В настоящее время существует два способа для доступа к HBase. В этом разделе описывается доступ с помощью оболочки HBase. Следующий раздел описывает использование пакета SDK для .NET.

Для большинства пользователей данные отображаются в табличном формате:

![табличные данные hdinsight hbase][img-hbase-sample-data-tabular]

В HBase, который является реализацией BigTable, те же данные выглядят следующим образом:

![данные bigtable hdinsight hbase][img-hbase-sample-data-bigtable]

Смысл операции прояснится после завершения следующей процедуры.


**Использование оболочки HBase**

1. Используйте RDP для подключения к кластеру HBase на HDInsight. Для получения инструкций см. статью [Управление кластерами Hadoop в HDInsight с использованием портала Azure][hdinsight-manage-portal].
2. В сеансе удаленного рабочего стола щелкните ярлык **Командная строка Hadoop**, расположенный на рабочем столе.
3. Откройте оболочку HBase:

		cd %HBASE_HOME%\bin
		hbase shell

4. Создайте HBase с двумя столбцами:

		create 'Contacts', 'Personal', 'Office'
		list
5. Вставьте какие-либо данные:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![оболочка hbase hdinsight hadoop][img-hbase-shell]

6. Получите одну строку

		get 'Contacts', '1000'

	Вы увидите те же результаты, что и при использовании команды сканирования, так как в таблице есть только одна строка.

	Дополнительную информацию о схеме таблицы Hbase см. в разделе [Введение в проектирование схемы HBase][hbase-schema]. Дополнительные команды HBase см. в [справочнике по Apache HBase][hbase-quick-start].


6. Выйдите из оболочки

		exit

**Для массовой загрузки данных в таблицу контактов HBase**

HBase включает несколько методов загрузки данных в таблицы. Для получения дополнительных сведений обратитесь к разделу [Массовая загрузка](http://hbase.apache.org/book.html#arch.bulk.load).


Образец файла данных был загружен в контейнер открытого BLOB-объекта wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. Файл данных содержит:

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

Можно создать текстовый файл и отправить его на собственную учетную запись хранения, если потребуется. Указания см. в разделе [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data].

> [AZURE.NOTE]Эта процедура использует таблицу контактов HBase, созданную в последней процедуре.

1. В сеансе удаленного рабочего стола щелкните ярлык **Командная строка Hadoop**, расположенный на рабочем столе.
2. Перейдите в каталог:

		cd %HBASE_HOME%\bin

3. Выполните следующую команду, чтобы преобразовать файл данных в StoreFiles и сохранить по относительному пути, указанному в Dimporttsv.bulk.output:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Выполните следующую команду для передачи данных из /example/data/storeDataFileOutput в таблицу HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Откройте оболочку HBase и выполните команду сканирования для получения списка содержимого таблицы.

## Проверка состояния кластера

HBase на HDInsight поставляется с веб-интерфейсом для наблюдения за кластерами. С помощью веб-интерфейса вы можете запросить статистику или сведения о регионах.

Чтобы открыть веб-интерфейс, необходимо выполнить RDP в кластер и затем щелкнуть по ярлыку HMaster Info Web UI на вашем рабочем столе или использовать следующий URL-адрес в веб-браузере:

	http://zookeeper[0-2]:60010/master-status

В высокодоступном кластере вы найдете ссылку на текущий активный главный узел HBase, на котором размещается веб-интерфейс.



## Использование HiveQL для формирования запросов к таблицам HBase

Можно запросить данные в таблицах HBase с помощью Hive. В данном разделе будет создана таблица Hive, которая сопоставляется с существующей таблицей HBase и используется для формирования запросов данных таблицы HBase.

**Открытие панели мониторинга кластера**

1. Перейдите по адресу **https://<HDInsightClusterName>.azurehdinsight.net/**.
5. Введите имя пользователя и пароль учетной записи Hadoop. По умолчанию имя пользователя **admin**, а пароль вы вводили в процессе подготовки. Откроется новая вкладка браузера.
6. Щелкните **Редактор Hive** в верхней части страницы. Редактор Hive выглядит следующим образом:

	![Панель мониторинга кластера HDInsight.][img-hdinsight-hbase-hive-editor]

**Выполнение запросов Hive**

1. Введите приведенный ниже сценарий HiveQL в окне редактора Hive и нажмите кнопку **Отправить**, чтобы создать таблицу Hive, сопоставляемую с таблицей HBase. Перед выполнением этого выражения убедитесь, что вы создали упомянутый в этом учебнике пример таблицы с помощью оболочки HBase.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Подождите, пока **Состояние** не сменится на **Завершено**.

2. Введите приведенный ниже сценарий HiveQL в редакторе Hive и нажмите кнопку **Отправить**. Запрос Hive запрашивает данные в таблице HBase:

     	SELECT count(*) FROM hbasecontacts;

4. Чтобы получить результат запроса Hive, щелкните ссылку **Подробнее** в окне **Сеанс задания** после окончания выполнения задания. Отобразится только один выходной файл задания, так как в таблицу HBase вы поместили одну запись.




**Просмотр выходного файла**

1. В консоли запросов щелкните **Браузер файлов**.
2. Выберите учетную запись хранения Azure, используемую в качестве файловой системы по умолчанию для кластера HBase.
3. Щелкните имя кластера HBase. Контейнер по умолчанию учетной записи хранилища Azure использует это имя кластера.
4. Щелкните **Пользователь**, а затем — **Admin**. (Это имя пользователя Hadoop.)
6. Щелкните имя задания, у которого значение **Последнее изменение** совпадает со временем выполнения запроса SELECT Hive.
4. Щелкните **stdout**. Сохраните файл и откройте его в Блокноте. Будет один выходной файл.

	![Браузер файлов редактора Hive для HBase в HDInsight][img-hdinsight-hbase-file-browser]

## Использование клиентской библиотеки .NET HBase REST API

Необходимо скачать клиентскую библиотеку REST API для HBase REST для .NET на портале GitHub и выполнить сборку проекта, чтобы вы могли использовать пакет SDK для HBase .NET. Описанная далее процедура включает в себя инструкции по выполнению этой задачи.

1. Создайте новое классическое консольное приложение Windows для Visual Studio на языке C#.
2. Откройте консоль диспетчера пакетов NuGet, последовательно выбрав меню **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
3. Выполните следующую команду NuGet в консоли:

		Install-Package Microsoft.HBase.Client

5. Добавьте в начало файла следующие операторы **using**:

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Замените функцию **Main** следующим кодом:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Задайте первые три переменные в функции **Main**.
8. Нажмите клавишу **F5** для запуска приложения.



## Что дальше?
В этом учебном материале по HBase для HDInsight вы узнали, как подготовить кластер HBase, а также как создавать таблицы и просматривать данные в этих таблицах из оболочки HBase. Вы также узнали, как использовать Hive для запроса данных из таблиц HBase и как использовать интерфейсы REST API на C# для HBase для создания таблицы HBase и извлечения данных из таблицы.

Дополнительные сведения см. в следующих статьях:

- [Обзор HDInsight HBase][hdinsight-hbase-overview] HBase — это разработанная Apache база данных NoSQL с открытым исходным кодом, созданная на основе Hadoop. HBase обеспечивает произвольный доступ и строгую согласованность для больших объемов неструктурированных и слабоструктурированных данных.
- [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet] Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно.
- [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md). Узнайте, как настроить репликацию HBase между двумя центрами обработки данных Azure.
- [Анализ мнений пользователей Twitter с использованием HBase в HDInsight][hbase-twitter-sentiment]. Узнайте, как проводить [анализ мнений](http://en.wikipedia.org/wiki/Sentiment_analysis) в режиме реального времени на основе больших данных с помощью HBase в кластере Hadoop в HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=Oct15_HO1-->