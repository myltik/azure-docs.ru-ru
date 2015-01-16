<properties title="Use custom activities in an Azure Data Factory pipeline" pageTitle="Использование настраиваемых действий в конвейере фабрики данных Azure" description="Узнайте, как создавать пользовательские действия и использовать их в конвейере фабрики данных Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Использование настраиваемых действий в конвейере фабрики данных Azure
Фабрика данных Azure поддерживает встроенные действия, такие как **действие копирования** и **действие HDInsight**, используемые в конвейерах для перемещения и обработки данных. Вы также можете создать собственное настраиваемое действие с определенной логикой преобразования и обработки и использовать его в конвейере. В кластере HDInsight настраиваемое действие выполняется только как задание сопоставления, так что для настраиваемого действия в конвейере вам потребуется связать кластер HDInsight.
 
В этой статье описывается процесс создания настраиваемого действия и методы его использования в конвейере фабрики данных Azure. Кроме того, в ней содержится подробное руководство с пошаговыми указаниями для создания и использования настраиваемого действия.

## Создание настраиваемого действия

Чтобы создать настраиваемое действие:
 
1.	Создайте проект **Библиотека классов** в Visual Studio 2013.
2.	Скачайте [пакет NuGet для фабрики данных Azure][nuget-package] и установите его. Указания по установке пакета содержатся в руководстве.
3. Добавьте следующие инструкции с оператором using в начало исходного файла в библиотеке классов.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Обновите класс для реализации интерфейса **ICustomActivity**.
	<ol type='a'>
		<li>
			Определите класс, производный от  <b>ICustomActivity</b>.
			<br/>
			Пример: <br/>
			общий класс <b> MyCustomActivity: ICustomActivity</b>
		</li>

		<li>
			Реализуйте метод <b>Execute</b> интерфейса <b>ICustomActivity</b>.
		</li>

	</ol>
5. Скомпилируйте проект.


## Использование настраиваемого действия в конвейере
Использование настраиваемого действия в конвейере

1.	**Запакуйте** все двоичные файлы из выходной папки **bin\debug** или **bin\release** для проекта. 
2.	**Отправьте ZIP-файл** в качестве большого двоичного объекта в** службу хранилища больших двоичных объектов Azure**. 
3.	Обновите **JSON-файл конвейера** для ссылки на ZIP-файл, библиотеку DLL для настраиваемого действия, класс действия и большой двоичный объект, содержащий ZIP-файл в JSON-файле конвейера. В JSON-файле:
	<ol type ="a">
		<li><b>для типа действия</b> должно быть установлено значение <b>CustomActivity</b>;</li>
		<li><b>AssemblyName</b> - это имя выходной библиотеки DLL из проекта Visual Studio;</li>
		<li><b>EntryPoint</b> определяет <b>пространство имен</b> и <b>имя</b> <b>класса</b>, реализующего интерфейс <b>ICustomActivity</b>;</li>
		<li><b>PackageLinkedService</b> - это связанная служба, которая относится к большому двоичному объекту, содержащему ZIP-файл; </li>
		<li><b>PackageFile</b> указывает расположение и имя ZIP-файла, отправленного в службу хранилища больших двоичных объектов Azure;</li>
		<li><b>LinkedServiceName</b> - это имя связанной службы, связывающей кластер HDInsight (по запросу или собственный) с фабрикой данных. В кластере HDInsight настраиваемое действие выполняется только как задание сопоставления.</li>
	</ol>

	

	**Пример части JSON-файла**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## Обновление настраиваемого действия
Если вы обновляете код для настраиваемого действия, создаете его и отправляете ZIP-файл, содержащий новые двоичные файлы, в службу хранилища больших двоичных объектов, необходимо переопределить существующий контейнер, который использует настраиваемое действие, запустив командлет **New-AzureDataFactoryPipeline**. Когда вы создаете конвейер, использующий настраиваемое действие, ZIP-файл копируется из указанной службы хранилища больших двоичных объектов в контейнер фабрики данных Azure в хранилище больших двоичных объектов, присоединенное к кластеру HDInsight. Это действие выполняется только при создании конвейера. Поэтому при обновлении настраиваемого действия вам необходимо восстановить конвейер. 

В следующем руководстве содержатся пошаговые указания для создания настраиваемого действия и его использования в конвейере фабрики данных Azure. Это руководство представляет собой расширенную версию учебника из статьи [Get started with Azure Data Factory][adfgetstarted] (Начало работы с фабрикой данных Azure). Если вы хотите увидеть, как работает настраиваемое действие, сначала ознакомьтесь с учебником по началу работы, а затем приступайте к этому руководству. 

## Пошаговое руководство
**Предварительные требования**


- Учебник из статьи [Get started with Azure Data Factory][adfgetstarted] (Начало работы с фабрикой данных Azure). Прежде чем продолжить работу с руководством, вам следует ознакомиться с этим учебником.
- Visual Studio 2012 или 2013
- Скачайте и установите пакет [Windows Azure SDK для .NET][azure-developer-center]
- Скачайте [пакеты NuGet для фабрики данных Azure][nuget-package].
- Скачайте и установите пакет NuGet для службы хранилища Azure. В руководстве содержатся указания, так что вы можете пропустить этот шаг.

### Шаг 1. Создать настраиваемое действие.

1.	Создайте проект библиотеки классов .NET:
	<ol type="a">
		<li>запустите <b>Visual Studio 2012</b> или <b>Visual Studio 2013</b>;</li>
		<li>щелкните <b>Файл</b>, наведите указатель мыши на <b>Создать</b> и щелкните <b>Проект</b>;</li> 
		<li>разверните <b>Шаблоны</b> и выберите <b>Visual C#</b>. В этом руководстве используется язык C#, но для создания настраиваемого действия вы можете использовать любой язык .NET.</li> ;
		<li>выберите тип <b>Библиотека классов</b> в списке типов проектов справа;</li>
		<li>в поле <b>Имя</b> введите <b>MyCustomActivity</b>;</li> 
		<li>в поле <b>Расположение</b> выберите <b>C:\ADFGetStarted</b>;</li>
		<li>Щелкните <b>ОК</b>, чтобы создать проект.</li>
	</ol>
2.  Щелкните <b>Инструменты</b>, наведите указатель мыши на <b>Диспетчер пакетов NuGet</b> и щелкните <b>Консоль диспетчера пакетов</b>.
3.	В <b>Консоли диспетчера пакетов</b> выполните следующую команду, чтобы импортировать пакет <b>Microsoft.Azure.Management.DataFactories</b>, скачанный ранее. Введите вместо имени папки расположение, в котором содержится скачанный пакет NuGet для фабрики данных.

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	В <b>Консоли диспетчера пакетов</b> выполните следующую команду, чтобы импортировать пакет <b>Microsoft.DataFactories.Runtime.</b> you downloaded earlier. Введите вместо имени папки расположение, в котором содержится скачанный пакет NuGet для фабрики данных.

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Импортируйте пакет NuGet для службы хранилища Windows Azure в проект.

		Install-Package WindowsAzure.Storage

5. Добавьте следующие инструкции с оператором **using** в исходный файл в проекте.

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Измените имя для параметра **namespace** на **MyCustomActivityNS**.

		namespace MyCustomActivityNS

7. Измените имя для класса на **MyCustomActivity** и извлеките класс из интерфейса **ICustomActivity**, как показано ниже.

		public class MyCustomActivity : ICustomActivity

8. Реализуйте (добавьте) метод **Execute** интерфейса **ICustomActivity** к классу **MyCustomActivity** и скопируйте следующий пример кода в метод. 

	Параметры **inputTables** и **outputTables** представляют собой входные и выходные таблицы для действия, соответствующего имени. Вы можете увидеть сообщения, записываемые в журнал, используя в файле журнала объект **logger**, который вы можете скачать с портала Azure, или командлеты. В словаре **extendedProperties** содержится список расширенных свойств, которые вы указываете в JSON-файле для действия, и их значения. 

	Следующий пример кода считает количество строк во входном большом двоичном объекте и выводит в выходном большом двоичном объекте следующее содержимое: путь к большому двоичному объекту, количество строк в большом двоичном объекте, имя компьютера, на котором выполняется действие, текущие дата и время.

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

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
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
                                    }
                                    count++;
                                }

                            }

                        }
                        output += string.Format(CultureInfo.InvariantCulture, 
										"{0},{1},{2},{3}\n", 
										folderPath, 
										count, 
										Environment.MachineName, 
										DateTime.UtcNow);
                    }
                    continuationToken = result.ContinuationToken;

                } while (continuationToken != null);
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. Добавьте следующие вспомогательные методы. Эти вспомогательные методы вызывает метод **Execute**. Метод **GetConnectionString** извлекает строку подключения к службе хранилища Azure, а **GetFolderPath** - расположение большого двоичного объекта. 


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. Скомпилируйте проект. В меню щелкните **Построить** и **Построить решение**.
11. Запустите **Проводник Windows** и перейдите к папке **bin\debug** или **bin\release** в зависимости от типа сборки.
12. Создайте ZIP-файл **MyCustomActivity.zip**, который содержит все двоичные файлы из папки <project folder>\bin\Debug.
	![zip output binaries][image-data-factory-zip-output-binaries]
13. Отправьте файл **MyCustomActivity.zip** в качестве большого двоичного объекта в контейнер больших двоичных объектов **customactvitycontainer** в службе хранилища больших двоичных объектов Azure, используемый связанной службой **MyBlobStore** в **ADFTutorialDataFactory**.   Создайте контейнер больших двоичных объектов **blobcustomactivitycontainer**, если он еще не создан. 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### Создание связанной службы для   кластера HDInsight, который будет использоваться для выполнения настраиваемого действия
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. Если вы используете собственный кластер HDInsight, он сразу сможет обработать срез. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера. Давайте в качестве примера используем кластер по запросу. 

> [WACOM.NOTE] Если вы ознакомились с учебником [Get started with Azure Data Factory][adfgetstarted] (Начало работы с фабрикой данных Azure) и руководством из статьи [Use Pig and Hive with Azure Data Factory][hivewalkthrough] (Использование сценариев Pig и Hive с фабрикой данных Azure), вы можете пропустить создание этой связанной службы и использовать связанную службу, которая доступна в ADFTutorialDataFactory. 

#### Использование кластера HDInsight по запросу

1. Создайте JSON-файл **HDInsightOnDemandCluster.json** со следующим содержимым и сохраните его в папку **C:\ADFGetStarted\Custom**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Запустите модуль **Azure PowerShell** и выполните следующую команду для перехода в режим **AzureResourceManager**. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Перейдите к папке с путем **C:\ADFGetstarted\Custom**.
4. Выполните следующую команду, чтобы создать связанную службу для кластера HDInsight по запросу.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### Использование собственного кластера HDInsight 

1. Создайте JSON-файл **MyHDInsightCluster.json** со следующим содержимым и сохраните его в папку **C:\ADFGetStarted\Custom**. Прежде чем сохранить JSON-файл, замените clustername, username и password соответствующими значениями.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Запустите модуль **Azure PowerShell** и выполните следующую команду для перехода в режим **AzureResourceManager**. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Перейдите к папке с путем **C:\ADFGetstarted\Custom**.
4. Выполните следующую команду, чтобы создать связанную службу для кластера HDInsight по запросу.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### Шаг 2. Использование настраиваемого действия в конвейере
Давайте расширим учебник из статьи [Начало работы с фабрикой данных Azure][adfgetstarted], чтобы создать еще один конвейер для проверки этого настраиваемого действия.

#### Создание связанной службы для кластера HDInsight, используемого для выполнения настраиваемого действия


1.	Создайте JSON-файл для конвейера, как показано в следующем примере, и сохраните его как **ADFTutorialPipelineCustom.json** в папке **C:\ADFGetStarted\Custom**. Измените имя **LinkedServiceName** на имя кластера HDInsight (**HDInsightOnDemandCluster** или **MyHDInsightCluster**).


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
                        	"Concurrency": 1,
                            "ExecutionPriorityOrder": "OldestFirst",
                            "Retry": 3,
                            "Timeout": "00:30:00",
                            "Delay": "00:00:00"		
						}
					}
				]
			}
		}

	Обратите внимание на следующее: 

	- в разделе действий содержится одно действие типа **CustomActivity**;
	- используйте ту же входную таблицу **EmpTableFromBlob**, которую вы использовали в учебнике по началу работы;
	- используйте новую выходную таблицу **OutputTableForCustom**, которую вы создадите в следующем шаге;
для 	- **AssemblyName** установлено имя библиотеки DLL **MyActivities.dll**;
для 	- **EntryPoint** установлено значение службы **MyCustomActivityNS.MyCustomActivity**;
для 	- **PackageLinkedService** установлено значение хранилища **MyBlobStore**, созданного в ходе работы с учебником [Начало работы с фабрикой данных Azure][adfgetstarted]. Это хранилище больших двоичных объектов содержит ZIP-файл настраиваемого действия;
	- **Для PackageFile** установлено значение **customactivitycontainer/MyCustomActivity.zip**.
     

4. Создайте JSON-файл для выходной таблицы (**OutputTableForCustom**, на которую ссылается JSON-файл конвейера) и сохраните его как C:\ADFGetStarted\Custom\OutputTableForCustom.json.

		

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}

 	Выходное расположение - **adftutorial/customactivityoutput/ГГГГММДДЧЧ/**, где ГГГГММДДЧЧ - год, месяц, день и час создания среза. Подробную информацию см. в разделе [Developer Reference][adf-developer-reference] (Справочник разработчика). 

5. Выполните следующую команду**, чтобы создать выходную таблицу** в службе **ADFTutorialDataFactory**.
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. Теперь выполните следующую команду, чтобы **создать конвейер**. Вы создали JSON-файл конвейера в предыдущем шаге.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. Выполните следующую команду PowerShell, чтобы **установить период активности** для созданного конвейера.

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] Замените значение **StartDateTime** текущим днем, а **EndDateTime** - следующим днем. StartDateTime и EndDateTime являются значениями времени в формате UTC, и потому должны быть заданы в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z. Значение **EndDateTime** является необязательным, но мы используем его в этом учебнике. 
	> Если вы не укажете значение **EndDateTime**, оно будет рассчитываться по формуле **StartDateTime + 48 часов**. Чтобы конвейер выполнялся в течение неопределенного срока, укажите значение **9/9/9999** для **EndDateTime**.



8. Убедитесь, что выходные файлы создаются в хранилище больших двоичных объектов в контейнере **adftutorial**.

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. Если вы откроете выходной файл, вы увидите выходные данные, похожие на следующие:
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(расположение большого двоичного объекта, количество строк в большом двоичном объекте, узел, на котором выполнено действие, метка даты и времени).

10.	Используйте [портал Azure][azure-preview-portal] или командлеты Azure PowerShell, чтобы отслеживать состояние фабрики данных, конвейеров и наборов данных. Вы можете увидеть сообщения из **ActivityLogger** в коде для настраиваемого действия в журналах, которые можно скачать из портала или с помощью командлетов.

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
См. статью [Начало работы с фабриками данных Azure][adfgetstarted], чтобы просмотреть подробные шаги по мониторингу наборов данных и конвейеров.      
    
## См. также

Статья | Описание
------ | ---------------
[Использование конвейера для работы с локальными данными][use-onpremises-datasources] | В этой статье содержится руководство, в котором показаны способы копирования данных из локальной базы данных SQL Server в службу хранилища больших двоичных объектов Azure.
[Use Pig and Hive with Data Factory][use-pig-and-hive-with-data-factory]| (Использование сценариев Pig и Hive с фабрикой данных)В этой статье содержится руководство, в котором показано, как использовать действие HDInsight для выполнения сценариев Hive и Pig, чтобы обрабатывать входные данные для получения выходных данных. 
[Учебник. Move and process log files using Data Factory][adf-tutorial] | (Перемещение и обработка файлов журнала с помощью фабрики данных)В этой статье содержится полное руководство, в котором показана реализация сценария, приближенного к действующему, используя фабрику данных Azure для преобразования данных из файлов журнала в файлы HDInsight.
[Use custom activities in a Data Factory][use-custom-activities] | (Использование настраиваемых действий в фабрике данных)В этой статье содержится руководство с пошаговыми указаниями для создания настраиваемого действия и его использования в конвейере. 
[Monitor and Manage Azure Data Factory using PowerShell][monitor-manage-using-powershell] | (Мониторинг и управление фабрикой данных Azure с помощью службы PowerShell)В этой статье описывается мониторинг фабрики данных Azure с помощью командлетов Azure PowerShell. Вы можете использовать примеры в статье на ADFTutorialDataFactory.
[Troubleshoot Data Factory issues][troubleshoot] | (Устранение неполадок фабрики данных)В этой статье описываются способы устранения неполадок в фабрике данных Azure. Вы можете использовать руководство в этой статье на ADFTutorialDataFactory путем введения ошибки (удалив таблицу в базе данных Azure SQL). 
[Azure Data Factory Developer Reference][developer-reference] | (Справочник по фабрике данных Azure для разработчика)В справочнике разработчика содержатся исчерпывающие справочные данные о командлетах, JSON-сценариях, функциях и т. д. 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/ru-ru/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
