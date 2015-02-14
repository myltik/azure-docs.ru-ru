<properties 
	pageTitle="Настройка таблиц HBase и выполнение запросов к ним с помощью Hive в HDInsight - Azure" 
	description="Приступите к использованию HBase с Hadoop в HDInsight. Узнайте, как создавать таблицы HBase и выполнять запросы для них с помощью Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



# Настройка кластеров HBase и выполнение запросов к ним с помощью Hive на Hadoop в HDInsight

Узнайте, как создавать таблицы HBase и выполнять к ним запросы с помощью Hive на Hadoop в HDInsight. 

##Содержание

* [Что такое HBase?](#hbaseintro)
* [Необходимые условия](#prerequisites)
* [Подготовка кластеров HBase с помощью портала управления Azure](#create-hbase-cluster)
* [Управление таблицами HBase с помощью оболочки HBase](#create-sample-table)
* [Использование HiveQL для формирования запросов к таблицам HBase](#hive-query)
* [Использование клиентской библиотеки Microsoft HBase REST для управления таблицами HBase](#hbase-powershell)
* [Дополнительные материалы](#seealso)

##<a name="hbaseintro"></a>Что такое HBase?

HBase - это база данных NoSQL с низким уровнем задержки, позволяющая производить транзакционную обработку больших объемов данных. HBase предлагается в форме управляемого кластера, интегрированного в среду Azure. Кластеры настроены на хранение данных непосредственно в хранилище BLOB-объектов Azure, что обеспечивает небольшую задержку и повышенную гибкость в выборе соотношения производительность/стоимость. Это позволяет клиентам создавать интерактивные веб-сайты, работающие с большими наборами данных, создавать службы, которые хранят данные с датчиков и телеметрию миллионов конечных точек, и анализировать эти данные с помощью заданий Hadoop. Дополнительную информацию о базе данных HBase и сценариях, для которых она может использоваться, см. в разделе [Обзор HDInsight HBase][hdinsight-hbase-overview].

> [AZURE.NOTE] HBase (версия 0.98.0) доступна только для использования с кластерами HDInsight версии 3.1 в HDInsight (на основе Apache Hadoop и YARN версии 2.4.0). Для получения информации о версиях см. раздел [Новые возможности в версиях кластеров Hadoop, предоставляемых в HDInsight][hdinsight-versions].

##<a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure** Дополнительные сведения о получении подписки см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
- **Учетная запись хранения Azure** Указания см. в разделе [Создание учетной записи хранения][azure-create-storageaccount].
- **Рабочая станция**, на которой установлено программное обеспечение Visual Studio 2013. Указания см. в разделе [Установка Visual Studio](http://msdn.microsoft.com/ru-ru/library/e2h7fzkw.aspx)

##<a name="create-hbase-cluster"></a>Подготовка кластера HBase на портале Azure

В данном разделе описана подготовка кластера HBase с использованием портала Azure.


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Подготовка кластера HDInsight на портале управления Azure** 


1. Войдите на [портал управления Azure][azure-management-portal]. 
2. Щелкните **СОЗДАТЬ** в левой нижней части, а затем последовательно выберите **СЛУЖБЫ ДАННЫХ**, **HDINSIGHT** и **HBASE**. 
3. Введите значения **ИМЯ КЛАСТЕРА**, **РАЗМЕР КЛАСТЕРА**, "ПАРОЛЬ ПОЛЬЗОВАТЕЛЯ КЛАСТЕРА" и **УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ**.
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. щелкните значок галочки слева внизу для создания кластера HBase.


##<a name="create-sample-table"></a>Создание примера таблицы HBase в оболочке HBase
В данном разделе описано добавление и использование удаленного рабочего стола для доступа к оболочке HBase, использование оболочки HBase для создания таблицы HBase, добавления строк и вывода строк таблицы.

Предполагается, что описанная в первом разделе процедура вами выполнена и у вас уже имеется успешно созданный кластер HBase.

**Включение подключения к удаленному рабочему столу для кластера HBase**

1. На портале управления щелкните слева элемент **HDINSIGHT**, чтобы просмотреть список существующих кластеров.
2. Щелкните тот кластер HBase, где требуется открыть оболочку HBase.
3. Щелкните расположенный сверху элемент **КОНФИГУРАЦИЯ**.
4. Щелкните расположенный снизу элемент **ВКЛЮЧИТЬ УДАЛЕННЫЙ ДОСТУП**.
5. Введите имя пользователя и пароль для RDP.  Имя пользователя должно отличаться от имени пользователя кластера, которое вы использовали при подготовке кластера. Дата **Истекает** может отстоять от текущей на семь дней.
6. Установите флажок в правом нижнем углу, чтобы включить удаленный рабочий стол.
7. После включения RPD щелкните **ПОДКЛЮЧЕНИЕ** в нижней части вкладки **КОНФИГУРАЦИЯ** и следуйте инструкциям.

 
**Открытие оболочки HBase**

1. В сеансе удаленного рабочего стола выберите ссылку **Командная строка Hadoop**, расположенную на рабочем столе.

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

**Проверка состояния кластера в веб-интерфейсе HBase**
	
HBase также имеет веб-интерфейс пользователя, помогающий наблюдать за кластером, например, предоставляя статистику запросов или сведения об областях. В кластере HBase веб-интерфейс можно найти по адресу zookeepernode.


	http://zookeepernode:60010/master-status
	
В кластере HighAvailability (HA) находится ссылка на текущий активный главный узел HBase, где размещается веб-интерфейс.

**Создание массовой нагрузки на пример таблицы**

1. Создайте файл samplefile1.txt со следующими данными и отправьте его в расположение /tmp/samplefile1.txt хранилища BLOB-объектов Azure:

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. Измените папку на домашнюю папку HBase:
		
		cd %HBASE_HOME%\bin

3. Выполните ImportTsv:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. Загрузите выходные данные из предыдущей команды в HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>Использование Hive для формирования запросов к таблице HBase

Теперь, когда вы подготовили кластер HBase и создали таблицу HBase, вы можете запросить ее с помощью Hive. В данном разделе будет создана таблица Hive, которая сопоставляется существующей таблице HBase и используется для формирования запросов к таблице HBase.

**Открытие панели мониторинга кластера**

1. Войдите на [портал управления Azure][azure-management-portal]. 
2. На левой панели щелкните **HDINSIGHT**. Вы увидите список созданных кластеров, включая только что созданный в последнем разделе.
3. Щелкните имя кластера, в котором вы хотите запустить задание Hive.
4. Щелкните **КОНСОЛЬ ЗАПРОСОВ** в нижней части страницы, чтобы открыть панель мониторинга кластера. Откроется веб-страница на отдельной вкладке браузера.   
5. Введите имя пользователя и пароль учетной записи Hadoop.  По умолчанию имя пользователя **admin**, пароль вы вводили в процессе подготовки. Открывается новая вкладка браузера. 
6. Щелкните расположенный сверху элемент **Редактор Hive**. Редактор Hive выглядит следующим образом.

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**Выполнение запросов Hive**

1. Введите приведенный ниже скрипт HiveQL в окне редактора Hive и нажмите кнопку **ОТПРАВИТЬ** для сопоставления таблицы Hive с таблицей HBase. Перед выполнением этого скрипта убедитесь, что вы создали упомянутую таблицу sampletable в HBase с помощью оболочки HBase.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	Подождите, пока **Состояние** не обновиться до **Завершено**.
 
2. Введите приведенный ниже скрипт HiveQL в редактор Hive и нажмите кнопку **ОТПРАВИТЬ**. Запрос Hive запрашивает данные в таблице HBase:

     	SELECT count(*) FROM hbasesampletable;
 
4. Для получения результатов запроса Hive щелкните ссылку **Подробнее** в окне **Сеанс задания** после окончания выполнения задания. Результат задания должен быть равен 1, поскольку вы поместили в таблицу HBase всего одну запись.




**Просмотр выходного файла**

1. В консоли запросов нажмите расположенную сверху кнопку **Браузер файлов**.
2. Выберите учетную запись хранилища Azure, используемую в качестве файловой системы по умолчанию для кластера HBase.
3. Щелкните имя кластера HBase. Контейнер по умолчанию учетной записи хранилища Azure использует это имя кластера.
4. Щелкните элемент **пользователь**.
5. Щелкните элемент **администратор**. Это имя пользователя Hadoop.
6. Щелкните имя задания, у которого значение **Последнее изменение** совпадает со временем выполнения запроса SELECT Hive.
4. Щелкните **stdout**. Сохраните файл и откройте его в Блокноте. Выходные данные должны иметь значение 1.

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>Использование интерфейсов API для C# клиентской библиотеки HBase REST для .NET при создании таблицы HBase и получении из нее данных

Проект клиентской библиотеки Microsoft HBase REST для .NET необходимо скачать с GitHub и выполнить его сборку для использования с пакетом SDK для HBase .NET. Описанная далее процедура включает в себя инструкции по выполнению этой задачи.

1. Создайте новое классическое консольное приложение Windows для Visual Studio на языке C#.
2. Откройте консоль диспетчера пакетов NuGet, последовательно выбрав меню **Средства**, **Диспетчер пакетов NuGet**, **Консоль диспетчера пакетов**.
3. Выполните следующую команду NuGet в консоли:

	Install-Package Microsoft.HBase.Client

5. Добавьте в начало файла следующие инструкции using:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Замените функцию Main следующим кодом:

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

7. Задайте первые три переменные в функции Main.
8. Нажмите клавишу **F5** для запуска приложения.



##<a name="next"></a> Что дальше?
В этом уроке вы узнали, как подготовить кластер HBase, как создавать таблицы и просматривать данные в этих таблицах из оболочки HBase. Вы также узнали, как использовать Hive для запроса данных из таблиц HBase и как использовать API-интерфейсы HBase C# для создания и извлечения данных из таблицы HBase. 

Дополнительные сведения см. на следующих ресурсах:

- [Обзор HDInsight HBase][hdinsight-hbase-overview]:
HBase - это Apache база данных NoSQL с открытым исходным кодом, созданная на основе Hadoop, обеспечивающая случайную выборку и строгую согласованность для больших объемов неструктурированных и слабоструктурированных данных.
- [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]:
Благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям взаимодействовать с HBase непосредственно.
- [Анализ мнений пользователей Twitter с использованием HBase в HDInsight][hbase-twitter-sentiment]
Узнайте, как проводить [анализ мнений](http://en.wikipedia.org/wiki/Sentiment_analysis) в режиме реального времени на основе данных большого размера с помощью кластера Hadoop в HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png








<!--HONumber=42-->
