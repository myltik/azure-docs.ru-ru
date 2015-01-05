<properties urlDisplayName="Get Started" pageTitle="Настройка таблиц HBase и выполнение запросов к ним с помощью Hive в HDInsight - Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. Learn how to create HBase tables and query them using Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# Настройка кластеров HBase и выполнение запросов к ним с помощью Hive на Hadoop в HDInsight

Узнайте, как создавать таблицы HBase и выполнять к ним запросы с помощью Hive на Hadoop в HDInsight. 

##Содержание

* [Что такое HBase?](#hbaseintro)
* [Предварительные требования](#prerequisites)
* [Подготовка кластеров HBase с помощью портала управления Azure](#create-hbase-cluster)
* [Управление таблицами HBase с помощью оболочки HBase](#create-sample-table)
* [Использование HiveQL для формирования запросов к таблицам HBase](#hive-query)
* [Использование клиентской библиотеки Microsoft HBase REST для управления таблицами HBase](#hbase-powershell)
* [См. также](#seealso)

##<a name="hbaseintro"></a>Что такое HBase?

HBase - это база данных NoSQL с низким уровнем задержки, позволяющая производить транзакционную обработку больших объемов данных. HBase предлагается в форме управляемого кластера, интегрированного в среду Azure. Кластеры настроены на хранение данных непосредственно в хранилище BLOB-объектов Azure, что обеспечивает небольшую задержку и повышенную гибкость в выборе соотношения производительность/стоимость. Это позволяет клиентам создавать интерактивные веб-сайты, работающие с большими наборами данных, создавать службы, которые хранят данные с датчиков и телеметрию миллионов конечных точек, и анализировать эти данные с помощью заданий Hadoop. Дополнительную информацию о базе данных HBase и сценариях, для которых она может использоваться, см. в разделе [Обзор HDInsight HBase](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-hbase-overview/).

> [WACOM.NOTE] HBase (версия 0.98.0) доступна только для использования с кластерами HDInsight версии 3.1 в HDInsight (на основе Apache Hadoop и YARN версии 2.4.0). Для получения информации о версиях см. раздел [Новые возможности в версиях кластеров Hadoop, предоставляемых в HDInsight][hdinsight-versions]

##<a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure** Дополнительную информацию о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или[Бесплатное пробное использование][azure-free-trial].
- **Учетная запись хранения Azure** Указания см. в разделе [Создание учетной записи хранения][azure-create-storageaccount].
- **Рабочая станция**, на которой установлено программное обеспечение Visual Studio 2013. Указание см. в разделе [Установка Visual Studio](http://msdn.microsoft.com/ru-ru/library/e2h7fzkw.aspx).
- Скачайте [клиентскую библиотеку Microsoft HBase REST для .NET](https://github.com/hdinsight/hbase-sdk-for-net). 


##<a name="create-hbase-cluster"></a>Подготовка кластера HBase на портале Azure

В данном разделе описана подготовка кластера HBase с использованием портала Azure.


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Чтобы подготовить кластер HBase на портале Azure:** 


1. Войдите на [портал управления Azure][azure-management-portal]. 

2. щелкните **HDInsight** слева от списка состояний кластеров в вашей учетной записи, затем щелкните значок **+СОЗДАТЬ** слева внизу; 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. щелкните значок HDInsight во втором столбце слева и параметр HBase в следующем столбце; укажите значения ИМЯ КЛАСТЕРА и РАЗМЕР КЛАСТЕРА, имя учетной записи хранения и пароль для нового кластера HBase;
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. щелкните значок галочки слева внизу для создания кластера HBase.


##<a name="create-sample-table"></a>Создание примера таблицы HBase в оболочке HBase
В данном разделе описано добавление и использование удаленного рабочего стола для доступа к оболочке HBase, использование оболочки HBase для создания таблицы HBase, добавления строк и вывода строк таблицы.

Предполагается, что описанная в первом разделе процедура вами выполнена и у вас уже имеется успешно созданный кластер HBase.

**Включение подключения к удаленному рабочему столу для кластера HBase**

1. Чтобы включить возможность подключения к удаленному рабочему столу для кластера HDInsight, выберите созданный вами кластер HBase и щелкните вкладку **КОНФИГУРАЦИЯ**. Нажмите кнопку **ВКЛЮЧИТЬ УДАЛЕННЫЙ ДОСТУП** внизу страницы, чтобы включить возможность подключения к удаленному рабочему столу кластера.
2. Введите учетные данные и дату прекращения в мастере **НАСТРОЙКА УДАЛЕННОГО РАБОЧЕГО СТОЛА**, щелкните круг с галочкой справа внизу. (Эта операция может занять несколько минут.)
3. Чтобы подключиться к кластеру HDInsight, нажмите кнопку **ПОДКЛЮЧИТЬ** в нижней части вкладки **КОНФИГУРАЦИЯ**.

 
**Откройте оболочку HBase**

1. В сеансе удаленного рабочего стола щелкните по ссылке **Командная строка Hadoop**, расположенной на рабочем столе.

2. Измените папку на домашнюю папку HBase:
		
		cd %HBASE_HOME%\bin

3. Откройте оболочку HBase:

		hbase shell


**Создание таблицы, добавление и извлечение данных**

1. Создание таблицы:

		create 'sampletable', 'cf1'

2. Добавление строки в таблицу:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Вывод списка строк в таблице:
	
		scan 'sampletable'

##<a name="hive-query"></a>Использование Hive для формирования запросов к таблице HBase

Теперь, когда вы подготовили кластер HBase и создали таблицу, вы можете запросить его с помощью Hive. В данном разделе будет создана таблица Hive, которая сопоставляется существующей таблице HBase и используется для формирования запросов к таблице HBase.

**Вот как можно открыть панель мониторинга кластера:**

1. Войдите на [портал управления Azure][azure-management-portal]. 
2. На левой панели щелкните **HDINSIGHT**. Вы увидите список созданных кластеров, включая только что созданный в последнем разделе.
3. Щелкните имя кластера, в котором вы хотите запустить задание Hive.
4. Щелкните **УПРАВЛЕНИЕ КЛАСТЕРОМ** внизу страницы, чтобы открыть панель мониторинга кластера. Откроется веб-страница на отдельной вкладке браузера.   
5. Введите имя пользователя и пароль учетной записи Hadoop.  По умолчанию имя пользователя **admin**, пароль вы вводили в процессе подготовки.  Панель мониторинга имеет следующий вид:

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**Чтобы выполнить запрос Hive**

1. Чтобы создать таблицу Hive с отображением на таблицу HBase, введите приведенный ниже сценарий HiveQL в окне консоли Hive и нажмите кнопку **ОТПРАВИТЬ**. Убедитесь, что вы создали упомянутую таблицу в HBase с помощью оболочки HBase перед выполнением этого сценария.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. Чтобы выполнить запрос Hive к данным в HBase, введите приведенный ниже сценарий HiveQL в окне консоли Hive и нажмите кнопку **ОТПРАВИТЬ**.

     	SELECT count(*) FROM hbasesampletable;
 
4. Для получения результата запроса Hive щелкните ссылку **Подробнее** в окне **Сеанс задания** после окончания выполнения задания.


Примечание. Ссылка на оболочку HBase переключает вкладку на **Оболочку HBase Shell**.



**Для просмотра файла с результатами выполните следующие действия:**

1. В верхней части панели мониторинга кластера щелкните **Файлы**.
2. Щелкните **Templeton-Job-Status**.
3. Щелкните номер GUID, у которого указано самое последнее время изменения, чуть больше времени запуска задания, которые вы записали ранее. Запишите этот GUID.  Он понадобится в следующем разделе.
4. В файле **stdout** содержатся данные, которые потребуются в следующем разделе. Вы можете щелкнуть **stdout**, чтобы скачать копию файла данных, если хотите.

	
##<a name="hbase-powershell"></a>Использование интерфейсов API для C# клиентской библиотеки HBase REST для .NET при создании таблицы HBase и получении из нее данных

Проект клиентской библиотеки Microsoft HBase REST для .NET необходимо скачать с GitHub и выполнить его сборку для использования с пакетом SDK для HBase .NET. Описанная далее процедура включает в себя инструкции по выполнению этой задачи.

1. Скачайте [клиентскую библиотеку Microsoft HBase REST для .NET](https://github.com/hdinsight/hbase-sdk-for-net) , если вы еще не выполнили это предварительное требование.

2. Откройте файл Marlin.sln в Visual Studio (**Файл** > **Открыть** > **Проект/Решение ...**) из расположения, в которое он был скачан. Чтобы увидеть решение Marlin и соответствующий проект Microsoft.HBase.Client, выберите **Просмотр** > **Обозреватель решений**. Выполните сборку проекта **Marlin**, щелкнув его правой кнопкой мыши в **обозревателе решений** и выбрав **Собрать решение**. 

4. Создайте новое консольное приложение Visual C#. Извлеките полученные в результате сборки файлы Microsoft.HBase.Client.dll и protobuf.dll (из каталога ...\bin\debug\Microsoft.HBase.Client) и добавьте их в свой проект C#: Щелкните правой кнопкой мыши пункт**Ссылки**, выберите **Добавить ссылки...**, перейдите к этим двум сборкам и передайте их. [protobuf-net](http://code.google.com/p/protobuf-net/) - это реализация .NET двоичного сериализатора буферов протокола Google, используемого для передачи данных.

5. Добавьте в начало файла следующие инструкции using:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Создайте новый экземпляр клиента HBase с использованием учетных данных кластера и получите его версию:

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. Чтобы создать новую таблицу HBase, используйте этот код:

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. Вставьте данные в таблицу с этим кодом:

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. Для извлечения ячейки с ее ключом используйте этот код: 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. Выполните сканирование строк в таблице с использованием следующего кода:

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>Сводка
В этом уроке вы узнали, как подготовить кластер HBase, как создавать таблицы и просматривать данные в этих таблицах из оболочки HBase. Вы также узнали, как использовать Hive для запроса данных из таблиц HBase и как использовать API-интерфейсы HBase C# для создания и извлечения данных из таблицы HBase.

##<a name="next"></a> Что дальше?

[Обзор HDInsight HBase][hdinsight-hbase-overview]:
HBase - это Apache база данных NoSQL с открытым исходным кодом, созданная на основе Hadoop, обеспечивающая случайную выборку и строгую согласованность для больших объемов неструктурированных и слабоструктурированных данных.

[Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]:
Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно.

[Анализ мнений пользователей Twitter с использованием HBase в HDInsight][hbase-twitter-sentiment]:
Узнайте, как в режиме реального времени выполнять[анализ мнений пользователей](http://en.wikipedia.org/wiki/Sentiment_analysis) большого объема данных с помощью HBase в кластере Hadoop в HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35.1-->
