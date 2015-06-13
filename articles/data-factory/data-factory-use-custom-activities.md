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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Использование настраиваемых действий в конвейере фабрики данных Azure
Фабрика Azure данных поддерживает встроенные действия, такие как **Действие копирования** и **HDInsight действия** для использования в конвейерах для перемещения и обработки данных. Можно также создать пользовательское действие .NET с свою собственную логику для обработки и преобразования и использование действия в конвейере. Можно настроить действия для запуска с помощью **Azure HDInsight** кластера или **пакета Azure** службы.

В этой статье описывается процесс создания настраиваемого действия и методы его использования в конвейере фабрики данных Azure. Кроме того, в ней содержится подробное руководство с пошаговыми указаниями для создания и использования настраиваемого действия. Пошаговое руководство использует службу HDInsight связаны. Использование пакета Azure связанных службы вместо, создать связанные службы типа **AzureBatchLinkedService** и использовать его в разделе действия конвейера JSON (** linkedServiceName **). См. [связанные службы Azure пакета](#AzureBatch) раздел подробные сведения об использовании пакета Azure с пользовательское действие.

## Предварительные требования
Загрузите последнюю версию [пакет NuGet для фабрики Azure данных][nuget-package] и установить его. Инструкции приведены в [Пошаговое руководство](#SupportedSourcesAndSinks) в этой статье.

## Создание настраиваемого действия

Чтобы создать настраиваемое действие:
 
1.	Создание **библиотеки классов** проекта в Visual Studio 2013.
3. Добавьте следующие инструкции с оператором using в начало исходного файла в библиотеке классов.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Обновите класс для реализации **IDotNetActivity** интерфейса.
	<ol type='a'>
	<li>
		Наследуйте класс от <b>IDotNetActivity</b>.
		<br/>
		Пример: <br/>
		открытый класс <b>MyDotNetActivity: IDotNetActivity</b>
	</li>

	<li>
		Реализуйте <b>Execute</b> метод <b>IDotNetActivity</b> интерфейс
	</li>

</ol>
5. Скомпилируйте проект.


## С помощью пользовательского действия в конвейере
Использование настраиваемого действия в конвейере

1.	**Архивировать** все двоичные файлы из **bin\debug** или **bin\release** выходной папки проекта. 
2.	**Передача zip** файла в виде больших двоичных объектов для вашей **хранилище больших двоичных объектов**. 
3.	Обновление **конвейера JSON** файл для обращения к ZIP-файл, настраиваемое действие DLL, класс действия и большой двоичный объект, содержащий ZIP-файл в конвейере JSON. В JSON-файле:
	<ol type ="a">
	<li><b>Типа действия</b> должно быть равным <b>DotNetActivity</b>.</li>
	<li><b>AssemblyName</b> имя выходного DLL из проекта Visual Studio.</li>
	<li><b>EntryPoint</b> указывает <b>имен</b> и <b>имя</b> из <b>класс</b> реализующий <b>IDotNetActivity</b> интерфейса.</li>
	<li><b>PackageLinkedService</b> связанного служба, которая ссылается на большой двоичный объект, содержащий ZIP-файл. </li>
	<li><b>PackageFile</b> указывает расположение и имя ZIP-файла, который был передан в хранилище больших двоичных объектов.</li>
	<li><b>LinkedServiceName</b> имя связанного службы, которая связывает кластера HDInsight (по запросу или собственные) к производству данных. В кластере HDInsight настраиваемое действие выполняется только как задание сопоставления.</li>
</ol>**Частичное примеру для формата JSON**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## Обновление настраиваемого действия
При обновлении кода для пользовательского действия, соберите и передачи ZIP-файл, содержащий новые двоичные файлы в хранилище больших двоичных объектов.

## <a name="walkthrough" /> Пошаговое руководство
Это пошаговое руководство предоставляет пошаговые инструкции для создания настраиваемого действия и использование действия в конвейер фабрика данных Azure. В этом пошаговом руководстве расширяет учебник из [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Если вы хотите увидеть, как работает настраиваемое действие, сначала ознакомьтесь с учебником по началу работы, а затем приступайте к этому руководству.

**Предварительные требования:**


- Учебник из [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Перед выполнением этого пошагового руководства необходимо выполнить учебник из этой статьи.
- Visual Studio 2012 или 2013
- Загрузите и установите [Azure .NET SDK][azure-developer-center]
- Загрузите последнюю версию [пакет NuGet для фабрики Azure данных][nuget-package] и установить его. Инструкции приведены в этом пошаговом руководстве.
- Скачайте и установите пакет NuGet для службы хранилища Azure. В руководстве содержатся указания, так что вы можете пропустить этот шаг.

## Шаг 1: Создание настраиваемого действия

1.	Создайте проект библиотеки классов .NET:
	<ol type="a">
	<li>Запустите <b>Visual Studio 2012</b> или <b>Visual Studio 2013</b>.</li>
	<li>Щелкните <b>файл</b>, пункты <b>Создать</b>, и нажмите кнопку <b>проекта</b>.</li> 
	<li>Разверните <b>шаблоны</b>, и выберите <b>Visual C#</b>. В этом руководстве используется язык C#, но для создания настраиваемого действия вы можете использовать любой язык .NET.;</li> 
	<li>Выберите <b>библиотеки классов</b> в списке типов проектов справа.</li>
	<li>Введите <b>MyDotNetActivity</b> для <b>имя</b>.</li> 
	<li>Выберите <b>C:\ADFGetStarted</b> для <b>расположение</b>.</li>
	<li>Нажмите кнопку <b>ОК</b>, чтобы создать проект.</li>
</ol>
2.  Щелкните <b>средства</b>, пункты <b>Диспетчер пакетов NuGet</b>, и нажмите кнопку <b>консоли диспетчера пакетов</b>.
3.	В <b>консоли диспетчера пакетов</b>, выполните следующую команду для импорта <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	В <b>консоли диспетчера пакетов</b>, выполните следующую команду для импорта <b>Microsoft.DataFactories.Runtime</b>. Введите вместо имени папки расположение, в котором содержится скачанный пакет NuGet для фабрики данных.

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. Импортируйте пакет NuGet хранилища Azure в проект.

		Install-Package Azure.Storage

5. Добавьте следующий **с помощью** инструкции к исходному файлу в проекте.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Изменить имя **имен** для **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Измените имя класса для **MyDotNetActivity** и сделайте его производным от **IDotNetActivity** интерфейс, как показано ниже.

		public class MyDotNetActivity : IDotNetActivity

8. Реализуйте (Добавить) **Execute** метод **IDotNetActivity** интерфейс **MyDotNetActivity** класса и скопируйте в следующем образце кода в метод.

	 **InputTables** и **outputTables** параметров представляют таблицы ввода-вывода для действия, как имена. Чтобы увидеть сообщения журнала с помощью **средство ведения журнала** объект в файл журнала, который можно загрузить с портала Azure или с помощью командлетов.  **ExtendedProperties** словарь содержит список расширенных свойств, укажите в файле JSON для действия и их значения.

	В следующем примере кода подсчитывается количество строк в входного большого двоичного объекта и создает следующее содержимое в выходные данные больших двоичных объектов: путь к BLOB-объектов, число строк в большом двоичном объекте компьютера, на котором запущена действия, текущей даты и времени.

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
                                        "{0},{1},{2},{3},{4}\n",
                                        folderPath,
                                        inputBlob.Name,
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

9. Добавьте следующие вспомогательные методы.  **Execute** метод вызывает эти вспомогательные методы.  **GetConnectionString** метод извлекает строку подключения хранилища Azure и **GetFolderPath** метод извлекает расположение BLOB-объекта.


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
   


10. Скомпилируйте проект. Щелкните **построения** из меню и нажмите кнопку **Построить решение**.
11. Запустите **Проводник**, и перейдите к **bin\debug** или **bin\release** тип зависимости папки сборки.
12. Создание ZIP-файл **MyDotNetActivity.zip** содержащие двоичные файлы в <project folder>папке \bin\Debug.
13. Отправка **MyDotNetActivity.zip** как большой двоичный объект в контейнер больших двоичных объектов: **customactvitycontainer** в Azure хранилище BLOB-объектов, **MyBlobStore** связанные службы в **ADFTutorialDataFactory** использует. Создание контейнера больших двоичных объектов **blobcustomactivitycontainer** если он еще не существует. 


## Шаг 2: Использование настраиваемого действия в конвейере
Ниже приведены шаги, которые будут выполняться на этом шаге.

1. Создание связанной службы для кластера HDInsight, на котором будет запускаться настраиваемого действия, как задание только для карты. 
2. Создайте выходную таблицу, которая сформирует конвейера в этом образце.
3. Создание и запуск конвейера, который использует настраиваемое действие, созданный на шаге 1. 
 
### Создание связанной службы для кластера HDInsight, который будет использоваться для выполнения настраиваемого действия
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. Если вы используете собственный кластер HDInsight, он сразу сможет обработать срез. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера.

> [AZURE.NOTE]Во время выполнения экземпляра действия .NET выполняется только на один рабочий узел в кластере HDInsight; его невозможно расширить для запуска на нескольких узлах. Несколько экземпляров .NET действия могут выполняться параллельно на разных узлах кластера HDInsight.

Если было расширено [Приступая к работе с фабрикой данных Azure][adfgetstarted] с помощью пошагового руководства из учебника [Использование Pig и Hive с фабрикой данных Azure][hivewalkthrough], можно создавать связанные службы и использовать связанные службы, уже имеется в ADFTutorialDataFactory.


#### Использование кластера HDInsight по запросу

1. В **портала Azure**, щелкните **автора и развертывание** на домашней странице данных фабрики.
2. Щелкните в редакторе данных фабрики **новых вычислений** из командной строки и выберите **кластера HDInsight по требованию** меню.
2. Выполните следующие действия в скрипт JSON. 
	1. Для **clusterSize** свойство, укажите размер кластера HDInsight.
	2. Для **jobsContainer** свойство, укажите имя контейнера по умолчанию, где будет храниться журнал кластера. В целях этого учебника укажите **adfjobscontainer**.
	3. Для **timeToLive** свойство, укажите, как долго клиент может простаивать перед его удалением. 
	4. Для **версии** свойство, укажите версию HDInsight, вы хотите использовать. Если исключить это свойство используется последняя версия.  
	5. Для **linkedServiceName**, укажите **StorageLinkedService** был создан в Get учебник работы. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Щелкните **Развернуть** на панели команд для развертывания связанной службы.
   
#### Использование собственного кластера HDInsight 

1. В **портала Azure**, щелкните **автора и развертывание** на домашней странице данных фабрики.
2. В **данных фабрики редактора**, щелкните **новых вычислений** из командной строки и выберите **кластера HDInsight** в меню.
2. Выполните следующие действия в скрипт JSON. 
	1. Для **clusterUri** свойство, введите URL-адрес для вашей HDInsight. Например: https://<clustername>.azurehdinsight.net/     
	2. Для **пользователя** свойство, введите имя пользователя, который имеет доступ к кластеру HDInsight.
	3. Для **пароль** свойство, введите пароль для пользователя. 
	4. Для **LinkedServiceName** свойство, введите **StorageLinkedService**. Это связанные службы, с которой был создан в учебнике работы Get. 

2. Щелкните **Развернуть** на панели команд для развертывания связанной службы.

### Создать выходную таблицу

1. В **данных фабрики редактора**, щелкните **новый набор данных**, и нажмите кнопку **хранилища BLOB-объектов Azure** на панели команд.
2. Замените следующий скрипт JSON скрипта JSON в правой области.

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


 	Расположение вывода **customactivityoutput/adftutorial/YYYYMMDDHH/** где YYYYMMDDHH года, месяца, даты и часа, производимых фрагмента. В разделе [Справочник разработчика][adf-developer-reference] подробные сведения.

2. Щелкните **Развернуть** на панели команд, чтобы развернуть таблицу.


### Создание и выполнение конвейера, который использует настраиваемое действие
   
1. Щелкните в редакторе данных фабрики **новому** на панели команд. Если команда не отображается, нажмите кнопку **... (Многоточие)** Чтобы просмотреть его. 
2. Замените следующий скрипт JSON JSON в правой области. Если требуется использовать собственный кластер и затем шаги для создания **HDInsightLinkedService** связанные службы, замените **HDInsightOnDemandLinkedService** с **HDInsightLinkedService** в следующий JSON. 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
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
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	> [AZURE.NOTE]Замените **StartDateTime** значение с трех дней до текущего дня и **EndDateTime** значение с текущего дня. StartDateTime и EndDateTime должны находиться в [Формат ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z. Согласно расписанию выходная таблица будет создаваться ежедневно, поэтому всего будет произведено три среза.

	Обратите внимание на следующее:

	- Существует одно действие в разделе «действия», и он имеет тип: **DotNetActivity**.
	- Использование одной и той же входной таблицы **EmpTableFromBlob** учебник работы, можно использовать в Get.
	- Использовать таблицу выходные данные **OutputTableForCustom** будет создан на следующем шаге.
	- **AssemblyName** присвоено имя DLL-Библиотеки: **MyActivities.dll**.
	- **EntryPoint** равен **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** равен **MyBlobStore** созданного в рамках данного учебника из [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Это хранилище больших двоичных объектов содержит ZIP-файл настраиваемого действия;
	- **PackageFile** равен **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Щелкните **Развернуть** на панели команд для развертывания в конвейер.
8. Убедитесь, что выходные файлы создаются в хранилище BLOB-объектов в **adftutorial** контейнера.

	![выходные данные настраиваемого действия][image-data-factory-ouput-from-custom-activity]

9. Если вы откроете выходной файл, вы увидите выходные данные, похожие на следующие:
	
	adftutorial/,EMP.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(BLOB-объект местоположение) (имя большого двоичного объекта), (количество строк в большой двоичный объект), (узел, на котором запущена действия), (дата отметка времени)

10.	Используйте [портала Azure][azure-preview-portal] или командлеты Azure PowerShell для мониторинга вашей данных фабрики, конвейеры и наборы данных. Чтобы увидеть сообщения из **ActivityLogger** в код для пользовательского действия в журналах можно загрузить с портала или с помощью командлетов.

	![Загрузка журналов из настраиваемого действия][image-data-factory-download-logs-from-custom-activity]
   
В разделе [Приступая к работе с фабрикой данных Azure][adfgetstarted] подробное описание шагов для мониторинга наборы данных и конвейеры.
    
## <a name="AzureBatch"></a> С помощью пакета Azure связанные службы 
> [AZURE.NOTE]В разделе [Технический обзор пакета Azure][batch-technical-overview] Обзор пакета Azure службы и в разделе [Приступая к работе с библиотекой пакета Azure для .NET][batch-get-started] Чтобы быстро приступить к работе со службой Azure пакета.

Ниже приведены общие шаги по с помощью пакета связанные службы Azure в этом пошаговом руководстве описано в предыдущем разделе.

1. Создайте учетную запись Azure пакета с помощью портала управления Azure. В разделе [Технический обзор пакета Azure][batch-create-account] инструкции. Запишите ключ учетной записи и имени учетной записи пакета Azure. 

	Можно также использовать [Создать AzureBatchAccount][new-azure-batch-account] командлет, чтобы создать учетную запись Azure пакета. В разделе [с помощью Azure PowerShell, чтобы управление учетной записью Azure пакета][azure-batch-blog] подробные инструкции по использованию этого командлета. 
2. Создайте пул пакета Azure. Можно загрузить и использовать [обозревателя пакета Azure][batch-explorer] (или) используйте [пакет библиотеки Azure для .NET][batch-net-library] для создания пула пакета Azure. В разделе [Пошаговое руководство по образцу обозреватель пакета Azure][batch-explorer-walkthrough] пошаговые инструкции по использованию обозревателя пакета Azure.
	
	Можно также использовать [Создать AzureBatchPool][new-azure-batch-pool] командлет, чтобы создать пул пакета Azure.

2. Создание пакета связанные службы Azure с помощью следующий шаблон JSON. Редактор фабрики данных отображает аналогичную шаблона можно начать с. Укажите имя учетной записи Azure пакета, имя ключа и пул учетной записи во фрагмент JSON.

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	В разделе [разделе MSDN связанные службы Azure пакета](https://msdn.microsoft.com/library/mt163609.aspx) для описания этих свойств.

2.  В редакторе фабрики данных откройте определение JSON для конвейера, созданный в пошаговом руководстве и замените **HDInsightLinkedService** с **AzureBatchLinkedService**.
3.  Может потребоваться изменить время начала и окончания для конвейера, что можно тестировать сценарий в службе Azure пакета. 
4.  Вы увидите Azure пакетные задачи, связанные с обработкой фрагменты в обозревателе пакета Azure, как показано на следующей схеме.

	![Azure пакетные задачи][image-data-factory-azure-batch-tasks]

## См. также

[Обновления данных фабрика azure: выполнение .NET ADF пользовательских действий, с помощью пакета Azure](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

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

<!---HONumber=GIT-SubDir--> 