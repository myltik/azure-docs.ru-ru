<properties
	pageTitle="Настройка таблиц HBase и выполнение запросов к ним с помощью Hive в HDInsight — Azure"
	description="Приступите к использованию HBase с Hadoop в HDInsight. Узнайте, как создавать таблицы HBase и выполнять запросы для них с помощью Hive."
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
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# Начало работы с Apache HBase в HDInsight

Узнайте, как создавать таблицы HBase и выполнять к ним запросы с помощью Hive в HDInsight.

HBase — это база данных NoSQL с низким уровнем задержки, позволяющая производить транзакционную обработку больших объемов данных. HBase предлагается в форме управляемого кластера, интегрированного в среду Azure. Кластеры настроены на хранение данных непосредственно в хранилище больших двоичных объектов Azure, что обеспечивает небольшую задержку и повышенную гибкость в выборе соотношения производительности и стоимости. Это позволяет клиентам создавать интерактивные веб-сайты, работающие с большими наборами данных, создавать службы, которые хранят данные с датчиков и телеметрию миллионов конечных точек, и анализировать эти данные с помощью заданий Hadoop. Дополнительную информацию о базе данных HBase и сценариях, для которых она может использоваться, см. в разделе [Обзор HDInsight HBase][hdinsight-hbase-overview].

> [AZURE.NOTE]HBase (версия 0.98.0) доступна только для использования с кластерами HDInsight версии 3.1 в HDInsight (на основе Apache Hadoop и YARN версии 2.4.0). Для получения информации о версиях ПО см. раздел [Новые возможности версий кластеров, предоставляемых HDInsight.][hdinsight-versions]

## Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure**: дополнительную информацию о получении подписки см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
- **Учетная запись хранения Azure**: указания см. в разделе [Создание учетной записи хранения][azure-create-storageaccount].
- **Рабочая станция** с установленным ПО Visual Studio 2013: указания см. в разделе [Установка Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Подготовка кластера HBase

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Подготовка кластера HBase с помощью портала Azure**


1. Войдите на [портал Azure][azure-management-portal].
2. Щелкните **СОЗДАТЬ** в левой нижней части страницы, а затем выберите **СЛУЖБЫ ДАННЫХ** > **HDINSIGHT** > **HBASE**.

	Также можно использовать параметр «НАСТРАИВАЕМОЕ СОЗДАНИЕ».
3. Введите значения **ИМЯ КЛАСТЕРА**, **РАЗМЕР КЛАСТЕРА**, «ПАРОЛЬ ПОЛЬЗОВАТЕЛЯ КЛАСТЕРА» и **УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ**.

	![Выбор типа кластера HBase и ввод учетных данных входа для кластера.][img-hdinsight-hbase-cluster-quick-create]

	Имя пользователя HTTP по умолчанию — admin. Имя можно настроить с помощью параметра «НАСТРАИВАЕМОЕ СОЗДАНИЕ».

	> [AZURE.WARNING]Для обеспечения высокой доступности служб HBase необходимо подготовить кластер, содержащий по крайней мере **три** узла. Это гарантирует, что если один узел выйдет из строя, области данных HBase будут доступны на других узлах.

4. Щелкните значок флажка внизу справа, чтобы создать кластер HBase.

>[AZURE.NOTE]После удаления кластера HBase можно создать другой кластер HBase, использовав тот же большой двоичный объект по умолчанию. Таблицы HBase, созданные в исходном кластере, получит новый кластер.

## Создание примера таблицы HBase в оболочке HBase
В этом разделе описывается, как с помощью оболочки HBase создавать таблицы HBase, добавлять строки и выводить список строк. Для доступа к оболочке HBase необходимо сначала включить протокол удаленного рабочего стола (RDP) и установить подключение к удаленному рабочему столу к кластеру HBase. Указания см. в статье [Управление кластерами Hadoop в HDInsight с использованием портала Azure][hdinsight-manage-portal].


**Использование оболочки HBase**

1. В сеансе удаленного рабочего стола щелкните ярлык **Командная строка Hadoop**, расположенный на рабочем столе.
2. Измените папку на домашнюю папку HBase:

		cd %HBASE_HOME%\bin
3. Откройте оболочку HBase:

		hbase shell

4. Создайте HBase с одним семейством столбцов и вставьте строку:

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Дополнительную информацию о схеме таблицы Hbase см. в разделе [Введение в проектирование схемы HBase][hbase-schema]. Дополнительные команды HBase см. в [справочнике по Apache HBase][hbase-quick-start].
5. Вывод списка таблиц HBase:

		list

**Проверка состояния кластера в веб-интерфейсе HBase**

HBase поставляется с веб-интерфейсом, который можно использовать для наблюдения за кластером. Например, можно запросить статистику или информацию о регионах. В кластере HBase веб-интерфейс можно найти по адресу zookeepernode:


	http://zookeepernode:60010/master-status

В высокодоступном кластере вы найдете ссылку на текущий активный главный узел HBase, где размещается веб-интерфейс.

**Массовой загрузка в пример таблицы**

1. Из оболочки HBase создайте таблицу HBase с двумя семействами столбцов:

		create 'Contacts', 'Personal', 'Office'


3. Создайте список контактов, содержащий следующие данные, и передайте его в папку /tmp/contacts.txt в хранилище больших двоичных объектов Azure. Указания см. в разделе [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data].

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. В командной строке Hadoop измените папку на домашний каталог HBase:

		cd %HBASE_HOME%\bin

3. Запустите ImportTsv. ImportTsv — это инструмент, который загрузит данные в формате TSV в HBase. Он используется для двух разных операций: загрузка данных из формата TSV в распределенную файловую систему (HDFS) Hadoop в HBase и подготовка файлов для загрузки. Дополнительную информацию см. в [справочнике по Apache HBase][hbase-reference].

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. Загрузите выходные данные от предыдущей команды в HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Использование Hive для формирования запросов к таблице HBase

Теперь, подготовив кластер HBase и создав таблицу HBase, вы можете запросить данные таблицы с помощью Hive. В данном разделе будет создана таблица Hive, которая сопоставляется с существующей таблицей HBase и используется для формирования запросов данных таблицы HBase.

**Открытие панели мониторинга кластера**

1. Войдите на [портал Azure][azure-management-portal].
2. На левой панели щелкните **HDINSIGHT**. Вы увидите список кластеров, включая созданный вами в последнем разделе.
3. Щелкните имя кластера, в котором вы хотите запустить задание Hive.
4. Щелкните **КОНСОЛЬ ЗАПРОСОВ** в нижней части страницы, чтобы открыть панель мониторинга кластера. В новой вкладке браузера откроется веб-страница.
5. Введите имя пользователя и пароль учетной записи Hadoop. По умолчанию имя пользователя **admin**, а пароль вы вводили в процессе подготовки. Откроется новая вкладка браузера.
6. Щелкните **Редактор Hive** в верхней части страницы. Редактор Hive выглядит следующим образом:

	![Панель мониторинга кластера HDInsight.][img-hdinsight-hbase-hive-editor]





























**Выполнение запросов Hive**

1. Введите приведенный ниже сценарий HiveQL в окне редактора Hive и нажмите кнопку **ОТПРАВИТЬ**, чтобы создать таблицу Hive, сопоставляемую с таблицей HBase. Перед выполнением этого оператора убедитесь, что вы создали упомянутый в этом учебнике пример таблицы с помощью оболочки HBase.

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Подождите, пока **Состояние** не сменится на **Завершено**.

2. Введите приведенный ниже сценарий HiveQL в редакторе Hive и нажмите кнопку **ОТПРАВИТЬ**. Запрос Hive запрашивает данные в таблице HBase:

     	SELECT count(*) FROM hbasecontactstable;

4. Чтобы получить результат запроса Hive, щелкните ссылку **Подробнее** в окне **Сеанс задания** после окончания выполнения задания. Отобразится только один выходной файл задания, так как в таблицу HBase вы поместили одну запись.




**Просмотр выходного файла**

1. В консоли запросов щелкните **Браузер файлов**.
2. Выберите учетную запись хранения Azure, используемую в качестве файловой системы по умолчанию для кластера HBase.
3. Щелкните имя кластера HBase. Контейнер по умолчанию учетной записи хранилища Azure использует это имя кластера.
4. Щелкните **Пользователь**, а затем — **Admin**. (Это имя пользователя Hadoop.)
6. Щелкните имя задания, у которого значение **Последнее изменение** совпадает со временем выполнения запроса SELECT Hive.
4. Щелкните **stdout**. Сохраните файл и откройте его в Блокноте. Будет один выходной файл.

	![Браузер файлов редактора Hive для HBase в HDInsight][img-hdinsight-hbase-file-browser]

## Использование клиентской библиотеки REST API для HBase REST для .NET C# для создания таблицы HBase и извлечения из нее данных

Необходимо скачать клиентскую библиотеку REST API для HBase REST для .NET на портале GitHub и выполнить сборку проекта, чтобы вы могли использовать пакет SDK для HBase .NET. Описанная далее процедура включает в себя инструкции по выполнению этой задачи.

1. Создайте новое классическое консольное приложение Windows для Visual Studio на языке C#.
2. Откройте консоль диспетчера пакетов NuGet, последовательно выбрав меню **СЕРВИС** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
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

            // Retrieve the cluster version
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
В этом учебнике вы узнали, как подготовить кластер HBase, а также как создавать таблицы и просматривать данные в этих таблицах из оболочки HBase. Вы также узнали, как использовать Hive для запроса данных из таблиц HBase и как использовать интерфейсы REST API на C# для HBase для создания таблицы HBase и извлечения данных из таблицы.

Дополнительные сведения см. на следующих ресурсах:

- [Обзор HDInsight HBase][hdinsight-hbase-overview]\: HBase — это NoSQL (нереляционная распределенная) база данных с открытым исходным кодом Apache, разработанная в рамках проекта Hadoop, которая обеспечивает произвольный доступ и строгую согласованность больших объемов неструктурированных и полуструктурированных данных.
- [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]\: благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения, поэтому эти приложения могут напрямую обмениваться данными с HBase.
- [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md): узнайте, как настроить репликацию HBase между двумя центрами обработки данных Azure. 
- [Анализ мнений Twitter с помощью HBase в HDInsight][hbase-twitter-sentiment]\: узнайте, как выполнять [анализ мнений](http://en.wikipedia.org/wiki/Sentiment_analysis) в режиме реального времени на основе больших данных, используя HBase в кластере Hadoop в HDInsight.

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
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->