<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="Пакет HDInsight SDK" pageTitle="Использование библиотек HDInsight .NET | Azure" metaKeywords="" description="Узнайте, как получить пакеты HDInsight NuGet и использовать их в приложении .NET." metaCanonical="" services="hdinsight" documentationCenter="" title="Использование пакета Hadoop .NET SDK с HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />





#Использование пакета Hadoop .NET SDK с HDInsight#

Пакет Hadoop .NET SDK предоставляет клиентские библиотеки .NET, которые упрощают работу с Hadoop в .NET. В этом учебнике вы узнаете, как получить пакет Hadoop .NET SDK и использовать его для создания простого приложения .NET, выполняющего запросы Hive с помощью службы Azure HDInsight. Имея файл actors.txt, вы напишете приложение для поиска актера или актрисы с большим количеством полученных наград. 

Чтобы включить HDInsight, щелкните [здесь](https://account.windowsazure.com/PreviewFeatures).

## В этой статье

* [Загрузка и установка пакета Hadoop .NET SDK](#install)
* [Подготовка к работе с учебником](#prepare)
* [Создание приложения](#create)
* [Выполнение приложения](#run)
* [Следующие шаги](#nextsteps)

##<a id="install"></a> Загрузка и установка пакета Hadoop .NET SDK##

Последнюю опубликованную сборку пакета SDK можно установить из [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Пакет SDK содержит следующие компоненты:

* **Библиотека MapReduce** - упрощает написание заданий MapReduce на языках .NET с использованием интерфейса потоковой передачи Hadoop.

* **Клиентская библиотека LINQ to Hive** - переводит запросы LINQ на C# или F# в запросы HiveQL и выполняет их в кластере Hadoop. Эта библиотека также может выполнять произвольные запросы HiveQL из приложения .NET.

* **Библиотека WebClient** - содержит клиентские библиотеки для *WebHDFS* и *WebHCat*.

	* **Клиентская библиотека WebHDFS** - работает с файлами в HDFS и хранилище BLOB-объектов Azure.

	* **Клиентская библиотека WebHCat** - управляет планированием и выполнением заданий в кластере HDInsight.
	
Синтаксис NuGet для установки библиотек:
	
	install-package Microsoft.Hadoop.MapReduce
	install-package Microsoft.Hadoop.Hive 
	install-package Microsoft.Hadoop.WebClient 
			
Эти команды добавляют библиотеки .NET и ссылки в текущий проект Visual Studio.

##<a id="prepare"></a> Подготовка к работе с учебником

Для продолжения вам потребуются [подписка Azure][free-trial]и [учетная запись хранения Azure][create-storage-account]. Необходимо также узнать ваше имя учетной записи хранения Azure и ключ учетной записи. Инструкции по получению этой информации см. в разделе *Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу* статьи [Управление учетными записями хранения](/ru-ru/manage/services/storage/how-to-manage-a-storage-account/).


Необходимо также загрузить файл Actors.txt, используемый в этом учебнике. Выполните следующие действия, чтобы загрузить этот файл в среду разработки:

1. Создайте папку C:\Tutorials на локальном компьютере.

2. Загрузите [Actors.txt](http://www.microsoft.com/ru-ru/download/details.aspx?id=37003) и сохраните файл в папку C:\Tutorials.

##<a id="create"></a>Создание приложения

В этом разделе вы узнаете, как передать файлы в кластер Hadoop программными средствами и как выполнить задания Hive, используя запросы LINQ to Hive.

1. Откройте Visual Studio 2012.

2. В меню "Файл" щелкните **Создать**, затем щелкните **Проект**.

3. В окне "Новый проект" введите или выберите следующие значения:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Свойство</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Значение</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Категория</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Шаблон</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Консольное приложение</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Имя</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
	</table>

4. Нажмите кнопку **ОК**, чтобы создать проект.


5. В меню **Сервис** щелкните **Диспетчер пакетов библиотеки**, щелкните **Консоль диспетчера пакетов**.

6. Выполните следующие команды в консоли, чтобы установить пакеты.

		install-package Microsoft.Hadoop.Hive 
		install-package Microsoft.Hadoop.WebClient 

	Эти команды добавляют библиотеки .NET и ссылки на них в текущий проект Visual Studio.

7. В обозревателе решений дважды щелкните **Program.cs**, чтобы открыть этот файл.

8. Добавьте в начало файла следующие инструкции:

		using Microsoft.Hadoop.WebHDFS.Adapters;
		using Microsoft.Hadoop.WebHDFS;
		using Microsoft.Hadoop.Hive;
	
9. В функции Main() скопируйте и вставьте следующий код:
		
		// Upload actors.txt to Blob Storage
		var asvAccount = [Storage-account-name.blob.core.windows.net];
		var asvKey = [Storage account key];
		var asvContainer = [Container name];
		var localFile = "C:/Tutorials/Actors.txt";
		var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
		var hadoopUserPassword = [Hadoop user password]; // The HDInsight cluster user password
		var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
		
		var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
		var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
		
		Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
		HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
		HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
		
		// Create Hive connection
		var hiveConnection = new HiveConnection(
		  new System.Uri(clusterURI),
		  hadoopUser, 
		  hadoopUserPassword,
		  asvAccount, 
		  asvKey);
		
		// Drop any existing tables called Actors
		// Only needed if you wish to rerun this application
		// and drop a previous Actors table.
		//hiveConnection.GetTable<HiveRow>("Actors").Drop();

		Console.WriteLine("Creating a Hive table named 'Actors'...");
		string command =
		  @"CREATE TABLE Actors(
		            MovieId string, 
		            ActorId string,
		            Name string, 
		            AwardsCount int) 
		            row format delimited fields 
		        terminated by ',';";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
		command =
		  @"LOAD DATA INPATH 
		        '/user/hadoop/MovieData/Actors.txt'
		    OVERWRITE INTO TABLE Actors;";
		hiveConnection.ExecuteHiveQuery(command).Wait();
		
		Console.WriteLine("Performing Hive query...");
		var result = hiveConnection.ExecuteQuery(@"select name, awardscount
		          from actors sort by awardscount desc
		          limit 1");
		result.Wait();

		Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
        Console.WriteLine("\nPress any key to continue.");
        Console.ReadKey();

10. Обновите константы в приложении. Служба Azure HDInsight использует хранилище BLOB-объектов Azure в качестве файловой системы по умолчанию. BLOB-объект обозначается как файловая система по умолчанию в процессе подготовки HDInsight. Вы можете использовать контейнер файловой системы по умолчанию или контейнер в другом хранилище BLOB-объектов. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight](/ru-ru/manage/services/hdinsight/howto-blob-store/).

	Если вы решите использовать контейнер файловой системы по умолчанию, вы найдете имя учетной записи хранения, ключ хранилища и имя контейнера в файле конфигурации *c:\apps\dist\hadoop-1.1.0-SNAPSHOT\conf>core-site.xml* при удаленном подключении к кластеру. Контейнер, используемый в качестве файловой системы по умолчанию, можно найти с помощью поискового термина *fs.default.name*. Чтобы найти имя учетной записи хранения и ключ учетной записи, выполните поиск *fs.azure.account.key*.
	
##<a id="run"></a>Выполнение приложения

Когда приложение открыто в Visual Studio, нажмите клавишу **F5** для запуска приложения. Должно открыться окно консоли, в котором отображаются выполняемые приложением действия по мере передачи данных, сохранения их в таблицу Hive и создания запросов. После завершения работы приложения и возврата результатов запроса нажмите любую клавишу, чтобы остановить приложение.

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Console Application")

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Выполнение приложением каждого действия может занять секунды или даже минуты. Время передачи файла Actors.txt будет зависеть от интернет-соединения с центром обработки данных Azure, а время выполнения остальных действий — от размера кластера.</p>
</div>

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Операция LOAD DATA INPATH является операцией перемещения, которая переносит данные Actors.txt в пространство имен файловой системы под управлением Hive. При этом файл Actors.txt фактически удаляется из места передачи. Поэтому файл Actors.txt необходимо передавать при каждом запуске этого приложения.</p>
<p>Дополнительные сведения о загрузке данных в Hive см. на сайте <a href="https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations">Hive GettingStarted</a>.</p>
</div>

##<a id="nextsteps"></a>Дальнейшие действия
Теперь вы знаете, как создать приложение .NET с помощью пакета Hadoop .NET SDK. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight](/ru-ru/manage/services/hdinsight/get-started-hdinsight/)
* [Использование Pig с HDInsight][hdinsight-pig] 
* [Использование MapReduce с HDInsight][hdinsight-mapreduce]
* [Использование Hive с HDInsight](/ru-ru/manage/services/hdinsight/using-hive-with-hdinsight/)

[hdinsight-pig]: /ru-ru/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-mapreduce]: /ru-ru/manage/services/hdinsight/using-mapreduce-with-hdinsight/



[free-trial]: http://www.windowsazure.com/ru-ru/pricing/free-trial/
[create-storage-account]: http://www.windowsazure.com/ru-ru/manage/services/storage/how-to-create-a-storage-account/



