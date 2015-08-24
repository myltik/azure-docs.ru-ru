<properties 
	pageTitle="Использование настраиваемых действий в конвейере фабрики данных Azure" 
	description="Узнайте, как создавать пользовательские действия и использовать их в конвейере фабрики данных Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="spelluru"/>

# Использование настраиваемых действий в конвейере фабрики данных Azure
Фабрика данных Azure поддерживает встроенные действия, такие как **Copy** и **HDInsight**, используемые в конвейерах для перемещения и обработки данных. Вы также можете создать собственное настраиваемое действие .NET с определенной логикой преобразования и обработки и использовать его в конвейере. Можно настроить действие для запуска с помощью кластера **Azure HDInsight** или **Пакетной службы Azure**.

В этой статье описывается процесс создания настраиваемого действия и методы его использования в конвейере фабрики данных Azure. Кроме того, в ней содержится подробное руководство с пошаговыми указаниями для создания и использования настраиваемого действия. В этом пошаговом руководстве используется связанная служба HDInsight. Чтобы использовать связанную пакетную службу Azure, создайте связанную службу типа **AzureBatchLinkedService** и используйте ее в разделе действия в конвейере JSON (**linkedServiceName**). Дополнительные сведения об использовании Пакетной службы Azure с пользовательским действием см. в разделе [Связанная Пакетная служба Azure](#AzureBatch).


## <a name="walkthrough" /> Пошаговое руководство
В этом руководстве содержатся пошаговые указания для создания настраиваемого действия и его использования в конвейере фабрики данных Azure. Это руководство представляет собой расширенную версию учебника из статьи [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Если вы хотите увидеть, как работает настраиваемое действие, сначала ознакомьтесь с учебником по началу работы, а затем приступайте к этому руководству.

**Предварительные требования:**


- Учебник из статьи [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Прежде чем продолжить работу с руководством, вам следует ознакомиться с этим учебником.
- Visual Studio 2012 или 2013
- Скачайте и установите пакет [Azure .NET SDK][azure-developer-center].
- Скачайте последний [пакет NuGet для фабрики данных Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) и установите его. Инструкции приведены в этом пошаговом руководстве.
- Скачайте и установите пакет NuGet для службы хранилища Azure. В руководстве содержатся указания, так что вы можете пропустить этот шаг.

## Шаг 1. Создание настраиваемого действия

1.	Создайте проект библиотеки классов .NET:
	<ol type="a">
	<li>запустите <b>Visual Studio 2012</b> или <b>Visual Studio 2013</b>;</li>
	<li>Щелкните <b>Файл</b>, наведите указатель мыши на пункт <b>Создать</b> и щелкните <b>Проект</b>.</li> 
	<li>Разверните раздел <b>Шаблоны</b> и выберите <b>Visual C#</b>. В этом руководстве используется язык C#, но для создания настраиваемого действия вы можете использовать любой язык .NET.;</li> 
	<li>выберите тип <b>Библиотека классов</b> в списке типов проектов справа;</li>
	<li>в поле <b>Имя</b> введите <b>MyDotNetActivity</b>;</li> 
	<li>в поле <b>Расположение</b> выберите <b>C:\ADFGetStarted</b>;</li>
	<li>Нажмите кнопку <b>ОК</b>, чтобы создать проект.</li>
</ol>
2.  Щелкните <b>Инструменты</b>, наведите указатель мыши на <b>Диспетчер пакетов NuGet</b> и щелкните <b>Консоль диспетчера пакетов</b>.
3.	В <b>консоли диспетчера пакетов</b> выполните следующую команду, чтобы импортировать пакет <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories

4. Импортируйте пакет NuGet для службы хранилища Azure в проект.

		Install-Package Azure.Storage

5. Добавьте следующие инструкции с **using** в исходный файл в проекте.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Измените имя для параметра **namespace** на **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Измените имя для класса на **MyDotNetActivity** и извлеките класс из интерфейса **IDotNetActivity**, как показано ниже.

		public class MyDotNetActivity : IDotNetActivity

8. Реализуйте (добавьте) метод **Execute** интерфейса **IDotNetActivity** к классу **MyDotNetActivity** и скопируйте следующий пример кода в метод.


	В следующем примере кода подсчитывается количество строк во входном большом двоичном объекте и выводит в выходном большом двоичном объекте следующее содержимое: путь к большому двоичному объекту, количество строк в большом двоичном объекте, имя компьютера, на котором выполняется действие, текущие дата и время.

        public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Table> tables, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Table inputTable = tables.Single(table => table.Name == activity.Inputs.Single().Name);
            inputLocation = inputTable.Properties.TypeProperties as CustomDataset;

			// using First method instead of Single since we are using the same 
			// Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;


            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputTable);

            logger.Write("Reading blob from: {0}", folderPath);

            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            BlobContinuationToken continuationToken = null;

            do
            {
                BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath,
                                            true,
                                            BlobListingDetails.Metadata,
                                            null,
                                            continuationToken,
                                            null,
                                            null);
                foreach (IListBlobItem listBlobItem in result.Results)
                {
                    CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                    int count = 0;
                    if (inputBlob != null)
                    {
                        using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                        {
                            while (!sr.EndOfStream)
                            {
                                string line = sr.ReadLine();
                                if (count == 0)
                                {
                                    logger.Write("First line: [{0}]", line);
                                }
                                count++;
                            }

                        }

                    }
                    output += string.Format(CultureInfo.InvariantCulture,
                                    "{0},{1},{2},{3},{4}\n",
                                    folderPath,
                                    inputBlob.Name,
                                    count,
                                    Environment.MachineName,
                                    DateTime.UtcNow);

                }
                continuationToken = result.ContinuationToken;

            } while (continuationToken != null);

            Table outputTable = tables.Single(table => table.Name == activity.Outputs.Single().Name);
            outputLocation = outputTable.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputTable);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. Добавьте следующие вспомогательные методы. Эти вспомогательные методы вызывает метод **Execute**. Метод **GetConnectionString** извлекает строку подключения к службе хранилища Azure, а **GetFolderPath** — расположение большого двоичного объекта.


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

        
        private static string GetFolderPath(Table dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }
   

10. Скомпилируйте проект. В меню щелкните **Построить** и **Построить решение**.
11. Запустите **Проводник Windows** и перейдите к папке **bin\\debug** или **bin\\release** в зависимости от типа сборки.
12. Создайте ZIP-файл **MyDotNetActivity.zip**, который содержит все двоичные файлы из папки <project folder>\\bin\\Debug. Можно включить файл MyDotNetActivity.pdb, чтобы получить дополнительные сведения, например номер строки в исходном коде, вызвавшей ошибку, в случае сбоя. 
13. Отправьте архив **MyDotNetActivity.zip** в качестве большого двоичного объекта в контейнер больших двоичных объектов **customactvitycontainer** из хранилища больших двоичных объектов Azure, используемого связанной службой **MyBlobStore** в **ADFTutorialDataFactory**. Создайте контейнер больших двоичных объектов **blobcustomactivitycontainer**, если он еще не создан. 


## Шаг 2. Использование настраиваемого действия в конвейере
Ниже приведены действия, которые будут выполняться в этом шаге.

1. Создание связанной службы для кластера HDInsight, на котором настраиваемое действие будет выполняться только как задание сопоставления. 
2. Создание выходной таблицы, которую сформирует конвейер в этом примере.
3. Создание и запуск конвейера, который использует настраиваемое действие, созданное вами на шаге 1. 
 
### Создание связанной службы для кластера HDInsight, который будет использоваться для выполнения настраиваемого действия
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. Если вы используете собственный кластер HDInsight, он сразу сможет обработать срез. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера.

> [AZURE.NOTE]Во время выполнения экземпляр действия .NET выполняется только на одном рабочем узле в кластере HDInsight; его невозможно расширить для выполнения на нескольких узлах. Несколько экземпляров действия .NET действия могут выполняться параллельно на разных узлах кластера HDInsight.

Если вы ознакомились с учебником [Приступая к работе с фабрикой данных Azure][adfgetstarted] и руководством из статьи [Использование Pig и Hive с фабрикой данных][hivewalkthrough], вы можете пропустить создание этой связанной службы и использовать связанную службу, которая доступна в ADFTutorialDataFactory.


#### Использование кластера HDInsight по запросу

1. На **портале Azure** на домашней странице фабрики данных щелкните **Зарегистрировать автора и выполнить развертывание**.
2. В редакторе фабрики данных щелкните **Создать вычисление** на панели команд и выберите в меню **Кластер HDInsight по требованию**.
2. В сценарии JSON выполните следующее. 
	1. В свойстве **clusterSize** укажите размер кластера HDInsight.
	2. В свойстве **jobsContainer** укажите имя контейнера, в котором по умолчанию будут сохраняться журналы кластера. Для целей данного учебника введите **adfjobscontainer**.
	3. В свойстве **timeToLive** укажите, как долго может простаивать клиент, прежде чем он будет удален. 
	4. В свойстве **version** укажите версию HDInsight, которую хотите использовать. Если исключить это свойство, будет использоваться последняя версия.  
	5. В качестве значения свойства **linkedServiceName** укажите службу **StorageLinkedService**, которую вы создали в учебнике по началу работы. 

		{ "name": "HDInsightOnDemandLinkedService", "properties": { "type": "HDInsightOnDemand", "typeProperties": { "clusterSize": "1", "timeToLive": "00:05:00", "version": "3.1", "linkedServiceName": "StorageLinkedService" } } }

2. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.
   
#### Использование собственного кластера HDInsight 

1. На **портале Azure** на домашней странице фабрики данных щелкните **Зарегистрировать автора и выполнить развертывание**.
2. В **редакторе фабрики данных** щелкните **Создать вычисление** на панели команд и выберите в меню **Кластер HDInsight**.
2. В сценарии JSON выполните следующее. 
	1. В свойстве **clusterUri** укажите URL-адрес вашего кластера HDInsight, Например, https://<clustername>.azurehdinsight.net/     
	2. В свойстве **UserName** введите имя пользователя, у которого есть доступ к кластеру HDInsight.
	3. В свойстве **Password** укажите пароль этого пользователя. 
	4. В свойстве **LinkedServiceName** введите **StorageLinkedService**. Это связанная служба, которая была создана в учебнике по началу работы. 

2. Нажмите кнопку **Развернуть** на панели команд, чтобы развернуть эту связанную службу.

### Создание выходной таблицы

1. В **редакторе фабрики данных** нажмите кнопку **Создать набор данных** и щелкните **Хранилище BLOB-объектов Azure** на панели команд.
2. Замените сценарий JSON в правой области на следующий:

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
		          }
		        }
		      ]
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

 	Выходное расположение — **adftutorial/customactivityoutput/ГГГГММДДЧЧ/**, где ГГГГММДДЧЧ — год, месяц, день и час создания среза. Подробную информацию см. в [Справочнике разработчика фабрики данных][adf-developer-reference].

2. Нажмите кнопку **Развернуть** на панели команд, чтобы развернуть таблицу.


### Создание и запуск конвейера, который использует настраиваемое действие
   
1. В редакторе фабрики данных, нажмите кнопку **Создать конвейер** на панели команд. Если команда не отображается, нажмите кнопку **... (многоточие)**, чтобы отобразить ее. 
2. Замените сценарий JSON в правой области на следующий: Если вы хотите использовать собственный кластер и выполнили все шаги по созданию связанной службы **HDInsightLinkedService**, замените значение **HDInsightOnDemandLinkedService** на **HDInsightLinkedService** в следующем JSON-файле. 
		
		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	Замените значение **StartDateTime** датой трехдневной давности, а значение **EndDateTime** — текущей датой. StartDateTime и EndDateTime необходимо указывать в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601) (например, 2014-10-14T16:32:41Z). Согласно расписанию выходная таблица будет создаваться ежедневно, поэтому всего будет произведено три среза.

	Обратите внимание на следующее:

	- в разделе действий содержится одно действие типа **DotNetActivity**;
	- используйте ту же входную таблицу **EmpTableFromBlob**, которую вы использовали в учебнике по началу работы;
	- используйте новую выходную таблицу **OutputTableForCustom**, которую вы создадите в следующем шаге;
	- для **AssemblyName** установлено имя библиотеки DLL **MyActivities.dll**;
	- для **EntryPoint** — **MyDotNetActivityNS.MyDotNetActivity**;
	- для **PackageLinkedService** установлено значение хранилища **MyBlobStore**, созданного в ходе работы с учебником [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Это хранилище больших двоичных объектов содержит ZIP-файл настраиваемого действия;
	- Для **PackageFile** установлено значение **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Нажмите кнопку **Развернуть** на панели команд, чтобы развернуть конвейер.
8. Убедитесь, что выходные файлы создаются в хранилище больших двоичных объектов в контейнере **adftutorial**.

	![выходные данные настраиваемого действия][image-data-factory-ouput-from-custom-activity]

9. Если вы откроете выходной файл, вы увидите выходные данные, похожие на следующие:
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(расположение большого двоичного объекта, название большого двоичного объекта, количество строк в большом двоичном объекте, узел, на котором выполнено действие, метка даты и времени).

10.	Используйте [портал Azure][azure-preview-portal] или командлеты Azure PowerShell для отслеживания состояния фабрики данных, конвейеров и наборов данных. Вы можете увидеть сообщения из **ActivityLogger** в коде для настраиваемого действия в журналах (а именно — user-0.log), которые можно скачать на портале или с помощью командлетов.

	![скачивание журналов из настраиваемого действия][image-data-factory-download-logs-from-custom-activity]
	
   
Подробное описание действий по мониторингу наборов данных и конвейеров см. в разделе[Мониторинг конвейеров и управление ими](data-factory-monitor-manage-pipelines.md).

## Обновление настраиваемого действия
Если вы обновляете код для настраиваемого действия, создайте его и отправьте ZIP-файл, содержащий новые двоичные файлы, в службу хранилища больших двоичных объектов.
    
## <a name="AzureBatch"></a> Использование связанной Пакетной службы Azure 
> [AZURE.NOTE]В статье [Технический обзор Пакетной службы Azure][batch-technical-overview] представлен обзор Пакетной службы Azure. Статья [Приступая к работе с Пакетной службой Azure для .NET][batch-get-started] поможет быстро начать работать с Пакетной службой Azure.

Можно выполнить пользовательские действия .NET с помощью пакетной службы Azure в виде вычислительного ресурса. Необходимо создать пулы пакетной службы Azure и указать число виртуальных машин, а также другие конфигурации. Пулы пакетной службы Azure предоставляют следующие возможности для клиентов.

1. Создание пулов, содержащих от одного до нескольких тысяч ядер.
2. Автоматическое масштабирование числа виртуальных машин по формуле
3. Поддержка виртуальных машин любого размера
4. Возможность настройки количества задач для виртуальной машины
5. Неограниченное количество задач в очереди


Ниже приведены общие шаги по использованию Пакетной службы Azure в пошаговом руководстве, описанном в предыдущем разделе.

1. На портале управления Azure создайте учетную запись Пакетной службы Azure. Соответствующие инструкции приведены в статье [Технический обзор Пакетной службы Azure][batch-create-account]. Запишите ключ и имя учетной записи Пакетной службы Azure. 

	Для создания учетной записи Пакетной службы Azure можно также воспользоваться командлетом [New-AzureBatchAccount][new-azure-batch-account]. Подробные инструкции по использованию этого командлета см. в записи блога [Использование Azure PowerShell для управления учетной записью Пакетной службы Azure][azure-batch-blog]. 
2. Создайте пул Пакетной службы Azure. Для создания пула пакетной службы Azure можно скачать и использовать [обозреватель пакетной службы Azure][batch-explorer] или воспользоваться [библиотекой пакетной службы Azure для .NET][batch-net-library]. Пошаговые инструкции по использованию обозревателя Пакетной службы Azure приведены в записи блога [Пошаговое руководство по обозревателю Пакетной службы Azure][batch-explorer-walkthrough].
	
	Для создания пула Пакетной службы Azure можно также воспользоваться командлетом [New-AzureBatchPool][new-azure-batch-pool].

2. Создайте связанную Пакетную службу Azure с помощью следующего шаблона JSON. В редакторе фабрики данных отображается аналогичный шаблон, который можно использовать для начала работы. Укажите во фрагменте JSON имя и ключ учетной записи Пакетной службы Azure, а также имя пула для Пакетной службы Azure.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.NOTE]Добавьте текст **.<region name** к имени учетной записи пакетной службы в свойстве **accountName**. Пример: mybatchaccount.eastus. Другой вариант — указать конечную точку batchUri, как показано ниже.

		accountName: "adfteam",
		batchUri: "https://eastus.batch.azure.com",
 
	Описания этих свойств см. в разделе MSDN, посвященном [связанной Пакетной службе Azure](https://msdn.microsoft.com/library/mt163609.aspx).

2.  В редакторе фабрики данных откройте определение JSON для конвейера, созданного в пошаговом руководстве, и замените **HDInsightLinkedService** на **AzureBatchLinkedService**.
3.  Может потребоваться изменить время начала и окончания для конвейера, чтобы можно было протестировать сценарий с использованием Пакетной службы Azure. 
4.  В обозревателе Пакетной службы Azure отобразятся задачи этой службы, связанные с обработкой срезов, как показано на снимке экрана ниже.

	![Задачи Пакетной службы Azure][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]Служба фабрики данных не поддерживает параметр по требованию для пакетной службы Azure, как для HDInsight. Пул пакетной службы Azure можно использовать только в фабрике данных Azure.

## См. также

[Обновления фабрики данных Azure: выполнение настраиваемых действий .NET ADF с помощью Пакетной службы Azure](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png
 

<!---HONumber=August15_HO7-->