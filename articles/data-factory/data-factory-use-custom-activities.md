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
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# Использование настраиваемых действий в конвейере фабрики данных Azure
Фабрика данных Azure поддерживает встроенные действия, такие как **Copy** и **HDInsight**, используемые в конвейерах для перемещения и обработки данных. Вы также можете создать собственное настраиваемое действие .NET с определенной логикой преобразования и обработки и использовать его в конвейере. Можно настроить действие для запуска с помощью кластера **Azure HDInsight** или **Пакетной службы Azure**.

В этой статье описывается процесс создания настраиваемого действия и методы его использования в конвейере фабрики данных Azure. Кроме того, в ней содержится подробное руководство с пошаговыми указаниями для создания и использования настраиваемого действия. В этом пошаговом руководстве используется связанная служба HDInsight. Чтобы использовать связанную пакетную службу Azure, создайте связанную службу типа **AzureBatchLinkedService** и используйте ее в разделе действия в конвейере JSON (**linkedServiceName**). Дополнительные сведения об использовании Пакетной службы Azure с пользовательским действием см. в разделе [Связанная Пакетная служба Azure](#AzureBatch).


## <a name="walkthrough" /> Пошаговое руководство
В этом руководстве содержатся пошаговые указания для создания настраиваемого действия и его использования в конвейере фабрики данных Azure. Это руководство представляет собой расширенную версию учебника из статьи [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Если вы хотите увидеть, как работает настраиваемое действие, сначала ознакомьтесь с учебником по началу работы, а затем приступайте к этому руководству.

### Предварительные требования


- Учебник из статьи [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Прежде чем продолжить работу с руководством, вам следует ознакомиться с этим учебником.
- Visual Studio 2012 или 2013
- Скачайте и установите пакет [Azure .NET SDK][azure-developer-center].
- Скачайте последний [пакет NuGet для фабрики данных Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) и установите его. Инструкции приведены в этом пошаговом руководстве.
- Скачайте и установите пакет NuGet для службы хранилища Azure. В руководстве содержатся указания, так что вы можете пропустить этот шаг.

### Пошаговые действия 
1.	**Создайте пользовательское действие** для использования в решении фабрики данных. Это действие содержит логику обработки данных. 
	1.	В Visual Studio (или редакторе кода) создайте проект библиотеки классов .NET, добавьте код для обработки входных данных и скомпилируйте проект.	
	2.	Запакуйте все двоичные файлы и PDB-файл (необязательно) в ZIP-архив в выходной папке.	
	3.	Отправьте ZIP-файл в хранилище BLOB-объектов Azure. Подробные указания см. в разделе «Создание пользовательского действия». 
2. **Создайте фабрику данных Azure, которая использует пользовательское действие**:
	1. Создание фабрики данных Azure.
	2. создание связанных служб.
		1. StorageLinkedService — предоставляет учетные данные хранилища для доступа к большим двоичным объектам.
		2. HDInsightLinkedService — определяет службу Azure HDInsight как среду выполнения приложений.
	3. Создайте наборы данных.
		1. InputDataset — определяет контейнер хранилища и папку для входных больших двоичных объектов.
		1. OuputDataset — определяет контейнер хранилища и папку для выходных больших двоичных объектов.
	2. Создайте конвейер, который использует пользовательское действие.
	3. Запустите и протестируйте конвейер.
	4. Выполните отладку конвейера.

## Создание пользовательского действия
Чтобы создать пользовательское действие .NET, которое можно использовать в конвейере фабрики данных Azure, необходимо создать проект **библиотеки классов .NET** с классом, который реализует интерфейс **IDotNetActivity**. У этого интерфейса только один метод — Execute. Подпись метода выглядит следующим образом:

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
У этого метода есть несколько ключевых компонентов, с которыми нужно разобраться.

- Метод принимает четыре параметра:
	- **linkedServices** — это перечисляемый список связанных служб, которые связывают источники входных и выходных данных (например, хранилище BLOB-объектов Azure) с фабрикой данных. В этом примере присутствует только одна связанная служба (служба хранилища Azure), которая используется для входных и выходных данных. 
	- **datasets** — это перечисляемый список наборов данных. Этот параметр можно использовать для получения расположений и схем, которые определяются входными и выходными наборами данных.
	- **activity** — этот параметр представляет текущую вычислительную сущность (в этом примере — Azure HDInsight).
	- **logger** — средство ведения журнала позволяет записывать комментарии отладки, которые отобразятся в виде пользовательского журнала для конвейера. 

- Этот метод возвращает словарь, который можно использовать для создания цепочки из пользовательских действий. В этом примере решения мы не будем использовать эту функцию.

### Процедура 
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
		using System.Linq;

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

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
			// to get extended properties (for example: SliceStart)
			DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

			// to log all extended properties			
			IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
			logger.Write("Logging extended properties if any...");
			foreach (KeyValuePair<string, string> entry in extendedProperties)
			{
				logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
			}
		

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
       			// with the data slice. 
        		// 
        	    // definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }

9. Добавьте следующие вспомогательные методы. Эти вспомогательные методы вызывает метод **Execute**. Метод **GetConnectionString** извлекает строку подключения к службе хранилища Azure, а **GetFolderPath** — расположение большого двоичного объекта. Самое главное, метод **Calculate** изолирует код, который выполняет итерацию каждого большого двоичного объекта.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.   
		/// </summary>

		private static string GetFolderPath(Dataset dataArtifact)
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

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
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

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

	Метод GetFolderPath возвращает путь к папке, на которую указывает набор данных, а метод GetFileName — имя большого двоичного объекта или файла, на который указывает набор данных.
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	Метод Calculate вычисляет количество экземпляров ключевого слова Microsoft во входных файлах (в больших двоичных объектах в папке). Условие поиска (Microsoft) жестко запрограммировано в коде.

10. Скомпилируйте проект. В меню щелкните **Построить** и **Построить решение**.
11. Запустите **Проводник Windows** и перейдите к папке **bin\\debug** или **bin\\release** в зависимости от типа сборки.
12. Создайте ZIP-файл **MyDotNetActivity.zip**, который содержит все двоичные файлы из папки <project folder>\\bin\\Debug. Можно также добавить файл **MyDotNetActivity.pdb**, чтобы в случае сбоя получить дополнительные сведения, например номер строки в исходном коде, вызвавшем ошибку.

	![Двоичные выходные файлы](./media/data-factory-use-custom-activities/Binaries.png)
13. Отправьте архив **MyDotNetActivity.zip** в качестве большого двоичного объекта в контейнер больших двоичных объектов **customactvitycontainer** из хранилища BLOB-объектов Azure, используемого связанной службой **StorageLinkedService** в **ADFTutorialDataFactory**. Создайте контейнер больших двоичных объектов **customactivitycontainer**, если он еще не создан.

> [AZURE.NOTE]Если добавить этот проект действия .NET в решение в Visual Studio, содержащее проект фабрики данных, то необязательно выполнять два последних шага по созданию ZIP-файла и вручную добавлять его в хранилище больших двоичных объектов Azure. При публикации сущностей фабрики данных с помощью Visual Studio эти шаги выполняются автоматически в процессе публикации. Сведения о создании и публикации сущностей фабрики данных с помощью Visual Studio см. в статьях [Создание первого конвейера с помощью Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) и [Копирование данных из большого двоичного объекта Azure в SQL Azure](data-factory-get-started-using-vs.md).

### Метод Execute

В этом разделе содержатся дополнительные сведения и примечания о коде в методе **Execute**.
 
1. Элементы для выполнения итерации входной коллекции находятся в пространстве имен [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). Для итерации коллекции больших двоичных объектов необходимо использовать класс **BlobContinuationToken**. В сущности, необходимо использовать цикл do-while и маркер в качестве механизма для выхода из цикла. Дополнительные сведения см. в статье [Использование хранилища BLOB-объектов из .NET](../storage/storage-dotnet-how-to-use-blobs.md). Базовый цикл выглядит так:

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{   
			// Get the list of input blobs from the input storage client object.
			BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    					              true,
    	                              BlobListingDetails.Metadata,
    	                              null,
    	                              continuationToken,
    	                              null,
    	                              null);
			// Return a string derived from parsing each blob.
    		output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
		} while (continuationToken != null);

	Дополнительные сведения см. в документации по методу [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx).

2.	Код для работы с набором больших двоичных объектов логически находится в цикле do-while. В методе **Execute** цикл do-while передает список больших двоичных объектов в метод **Calculate**. Этот метод возвращает строковую переменную с именем **output**, которая является результатом итерации всех больших двоичных объектов в сегменте.

	Он возвращает число вхождений условия поиска (**Microsoft**) в большом двоичном объекте, переданном в метод **Calculate**.

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	После завершения работы метода **Calculate** его результаты записываются в новый большой двоичный объект. Следовательно, для каждого обработанного набора больших двоичных объектов можно записать новый большой двоичный объект с результатами. Чтобы записать новый большой двоичный объект, сначала найдите выходной набор данных.

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	Код также вызывает вспомогательный метод **GetFolderPath**, чтобы получить путь к папке (имя контейнера хранилища).
 
			folderPath = GetFolderPath(outputDataset);

	Метод **GetFolderPath** приводит объект DataSet к AzureBlobDataSet со свойством FolderPath.
			
			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
			
			return blobDataset.FolderPath;

5.	Код вызывает метод **GetFileName**, чтобы получить имя файла (имя большого двоичного объекта). Используемый код аналогичен приведенному выше коду, используемому для получения пути к папке.

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	Чтобы записать имя файла, создается объект универсального кода ресурса (URI). Для возврата имени контейнера в конструкторе URI используется свойство **BlobEndpoint**. Для создания URI выходного большого двоичного объекта сочетаются путь к папке и имя файла.

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	Имя файла записано, и теперь можно записать выводимую строку из метода Calculate в новый большой двоичный объект:

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## Создание фабрики данных

В разделе **Создание настраиваемого действия** вы создали настраиваемое действие и отправили ZIP-файл с двоичными файлами и PDB-файлом в контейнер больших двоичных объектов Azure. В этом разделе вы создадите **фабрику данных** Azure с **конвейером**, который использует **настраиваемое действие**.
 
Входной набор данных для пользовательского действия представляет собой большие двоичные объекты (файлы) во входной папке (mycontainer\\inputfolder) в хранилище BLOB-объектов. Выходной набор данных для пользовательского действия представляет собой выходные большие двоичные объекты в выходной папке (mycontainer\\outputfolder) в хранилище BLOB-объектов.

Создайте файл с именем file.txt со следующим содержимым и отправьте его в mycontainer\\inputfolder (mycontainer — имя контейнера больших двоичных объектов Azure, а inputfolder — имя папки в этом контейнере).

	test custom activity Microsoft test custom activity Microsoft

Входная папка соответствует одному срезу в фабрике данных Azure, даже если она содержит 2 файла или более. При обработке каждого среза конвейером пользовательское действие выполняет итерацию всех больших двоичных объектов во входной папке для этого среза.

В папке mycontainer\\output отобразится один выходной файл с одной или несколькими строками (соответствует количеству больших двоичных объектов во входной папке):
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


Ниже коротко приведены шаги, подробно описанные далее в этом разделе.

1. Создание **фабрики данных**.
2. Создание **связанных служб** для кластера HDInsight, в котором будет выполняться пользовательское действие в качестве задания сопоставления, и службы хранилища Azure, в которой содержатся входные и выходные большие двоичные объекты. 
2. Создание входных и выходных **наборов данных**, которые представляют собой входные и выходные данные пользовательского действия. 
3. Создание и запуск **конвейера**, который использует пользовательское действие.

### Шаг 1. Создание фабрики данных

1.	Войдите на портал Azure и сделайте следующее:
	1.	В меню слева нажмите кнопку **Создать**.
	2.	В колонке **Создание** щелкните **Данные и аналитика**.
	3.	В колонке **Аналитика данных** щелкните **Фабрика данных**.
2.	В колонке **Создание фабрики данных** в поле «Имя» введите **CustomActivityFactory**. Имя фабрики данных Azure должно быть глобально уникальным. Если возникнет ошибка **Имя фабрики данных CustomActivityFactory недоступно**, измените имя этой фабрики данных (например, на **вашеимяCustomActivityFactory**) и попробуйте создать ее снова.
3.	Щелкните **Имя группы ресурсов**, чтобы выбрать существующую группу ресурсов, или создайте группу ресурсов. 
4.	Убедитесь, что используется именно та **подписка** и **регион**, в которых необходимо создать фабрику данных. 
5.	В колонке **Создание фабрики данных** нажмите кнопку **Создать**.
6.	Созданная фабрика данных появится на **панели мониторинга** портала Azure.
7.	После успешного создания фабрики данных ее содержимое отобразится в соответствующей колонке.

### Шаг 2. Создание связанных служб

Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. На этом этапе вы свяжете учетную запись службы хранилища Azure и кластер Azure HDInsight с фабрикой данных.

#### Создание связанной службы хранения Azure

1.	В колонке **ФАБРИКА ДАННЫХ** для **CustomActivityFactory** щелкните элемент **Создание и развертывание**. Откроется редактор фабрики данных.
2.	На панели команд щелкните **Создание хранилища данных** и выберите **Служба хранилища Azure**. В редакторе отобразится сценарий JSON для создания связанной службы хранилища Azure.
3.	Замените **account name** именем своей учетной записи хранения Azure, а **account key** — ключом доступа к учетной записи хранения Azure. Сведения о получении ключа доступа к хранилищу см. в разделах о [просмотре, копировании и повторном создании ключей доступа к хранилищу](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
4.	Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.


#### Создание связанной службы Azure HDInsight 
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. Если вы используете собственный кластер HDInsight, он сразу сможет обработать срез. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера.

> [AZURE.NOTE]Во время выполнения экземпляр действия .NET выполняется только на одном рабочем узле в кластере HDInsight; его невозможно расширить для выполнения на нескольких узлах. Несколько экземпляров действия .NET действия могут выполняться параллельно на разных узлах кластера HDInsight.

Если вы ознакомились с учебником [Приступая к работе с фабрикой данных Azure][adfgetstarted] и руководством из статьи [Использование Pig и Hive с фабрикой данных][hivewalkthrough], вы можете пропустить создание этой связанной службы и использовать связанную службу, которая доступна в ADFTutorialDataFactory.


##### Использование кластера HDInsight по запросу

1. На **портале Azure** на домашней странице фабрики данных щелкните **Зарегистрировать автора и выполнить развертывание**.
2. В редакторе фабрики данных щелкните **Создать вычисление** на панели команд и выберите в меню **Кластер HDInsight по требованию**.
2. В сценарии JSON выполните следующее.
	1. В свойстве **clusterSize** укажите размер кластера HDInsight.
	3. В свойстве **timeToLive** укажите, как долго может простаивать клиент, прежде чем он будет удален.
	4. В свойстве **version** укажите версию HDInsight, которую хотите использовать. Если исключить это свойство, будет использоваться последняя версия.  
	5. В качестве значения свойства **linkedServiceName** укажите службу **StorageLinkedService**, которую вы создали в учебнике по началу работы.

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

##### Использование собственного кластера HDInsight

1. На **портале Azure** на домашней странице фабрики данных щелкните **Зарегистрировать автора и выполнить развертывание**.
2. В **редакторе фабрики данных** щелкните **Создать вычисление** на панели команд и выберите в меню **Кластер HDInsight**.
2. В сценарии JSON выполните следующее.
	1. В свойстве **clusterUri** укажите URL-адрес вашего кластера HDInsight, Например, https://<clustername>.azurehdinsight.net/     
	2. В свойстве **UserName** введите имя пользователя, у которого есть доступ к кластеру HDInsight.
	3. В свойстве **Password** укажите пароль этого пользователя.
	4. В свойстве **LinkedServiceName** введите **StorageLinkedService**. Это связанная служба, которая была создана в учебнике по началу работы.

2. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

### Шаг 3. Создание наборов данных
На этом шаге вы создадите наборы данных, которые представляют входные и выходные данные.

#### Создание входного набора данных
1.	В **Редакторе** фабрики данных нажмите на панели инструментов кнопку **Создать набор данных** и выберите в раскрывающемся меню пункт **Хранилище больших двоичных объектов Azure**.
2.	Замените код JSON в правой области на следующий фрагмент кода JSON:

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "StorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	Позже в этом пошаговом руководстве вы создадите конвейер со временем начала 2015-11-16T00:00:00Z и временем окончания 2015-11-16T05:00:00Z. Данные будут создаваться почасово, поэтому мы получим 5 входных и выходных срезов (между **00**:00:00 -> **05**:00:00).

	Для параметров **frequency** и **interval** входного набора данных установлены значения **Hour** и **1**. Это означает, что входной срез данных будет создаваться каждый час. В этом примере используется тот же файл (file.txt) в папке intputfolder.

	Это значения времени начала для каждого среза, представленные системной переменной SliceStart в приведенном выше фрагменте кода JSON.

	
3.	На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть **InputDataset**. Убедитесь, что на панели заголовка редактора отображается сообщение **ТАБЛИЦА УСПЕШНО СОЗДАНА**.


#### Создание выходной таблицы

1. В **редакторе фабрики данных** нажмите кнопку **Создать набор данных** и щелкните **Хранилище BLOB-объектов Azure** на панели команд.
2. Замените сценарий JSON в правой области на следующий:

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
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

	Для каждого входного среза данных создается выходной большой двоичный объект или файл. Ниже в таблице приведены имена, которые даются выходным файлам для каждого среза. Все выходные файлы создаются в одной выходной папке: **adftutorial\\customactivityoutput**.

	| Срез | Время начала | Выходной файл |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	Помните, что все файлы во входной папке являются частью среза со значениями времени начала, указанными выше. Во время обработки этого среза пользовательское действие сканирует каждый файл и создает строку в выходном файле с количеством вхождений условия поиска (Microsoft). Если в папке inputfolder находятся три файла, в выходном файле будут содержаться три строки для каждого почасового среза: 2015-11-16-00.txt, 2015-11-16:01:00:00.txt и т. д...


2. На панели команд нажмите кнопку **Развернуть**, чтобы развернуть **OutputDataset**.


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
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
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
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
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
	- Для параметра **PackageLinkedService** задано значение **StorageLinkedService**, которое указывает на хранилище BLOB-объектов, содержащее ZIP-файл пользовательского действия. Если для входных и выходных файлов и ZIP-файла пользовательского действия используются разные учетные записи службы хранилища Azure, необходимо создать другую связанную службу хранилища Azure. В этой статье предполагается использование одной и той же учетной записи службы хранилища Azure.
	- Для **PackageFile** установлено значение **customactivitycontainer/MyDotNetActivity.zip**. Для его задания используется формат: <containerforthezip>/<nameofthezip.zip>.
	- Пользовательское действие принимает **InputDataset** в качестве входных данных и **OutputDataset** — в качестве выходных данных.
	- Свойство linkedServiceName пользовательского действия указывает на **HDInsightLinkedService**, которое сообщает фабрике данных Azure, что необходимо запустить пользовательское действие в кластере Azure HDInsight.
	- Для свойства **isPaused** по умолчанию установлено значение **false**. В этом примере конвейер запускается незамедлительно, потому что срезы приходятся на прошлое. Для этого свойства можно задать значение true, чтобы приостановить работу конвейера. Чтобы перезапустить его, нужно снова установить значение false. 
	- Разница между временем **начала** и временем **окончания** составляет **5** часов, а срезы создаются каждый час. Таким образом конвейер создает 5 срезов. 


4. Чтобы развернуть конвейер, нажмите кнопку **Развернуть** на панели команд.

### Мониторинг конвейера
 
8. На портале Azure в колонке «Фабрика данных» щелкните **Схема**.
	
	![Плитка "Схема"](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. Теперь в представлении схемы щелкните OutputDataset.
 
	![Представление схемы](./media/data-factory-use-custom-activities/diagram.png)

10. Если срезы уже созданы, вы увидите 5 выходных срезов данных в состоянии готовности.

	![Выходные срезы](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. Убедитесь, что выходные файлы создаются в хранилище больших двоичных объектов в контейнере **adftutorial**.

	![выходные данные настраиваемого действия][image-data-factory-ouput-from-custom-activity]

9. Если вы откроете выходной файл, вы увидите выходные данные, похожие на следующие:

	В файле inputfolder/2015-11-16-00/file.txt найдено 2 вхождения условия поиска Microsoft.

10.	Используйте [портал Azure][azure-preview-portal] или командлеты Azure PowerShell для отслеживания состояния фабрики данных, конвейеров и наборов данных. Вы можете просматривать сообщения **ActivityLogger** о настраиваемом действии в журналах (а именно — user-0.log), которые можно скачать на портале или с помощью командлетов.

	![скачивание журналов из настраиваемого действия][image-data-factory-download-logs-from-custom-activity]


Подробные указания по мониторингу наборов данных и конвейеров см. в статье [Мониторинг конвейеров и управление ими](data-factory-monitor-manage-pipelines.md).

### Выполнение отладки конвейера
Отладка состоит из нескольких базовых методов:

1.	Если для входного среза данных не установлено значение **Ready**, убедитесь, что структура входной папки правильная и что в ней существует файл file.txt.
2.	В методе **Execute** пользовательского действия используйте объект **IActivityLogger**, чтобы записывать в журнал сведения, которые помогут устранить неполадки. Сообщения, записанные в журнал, отобразятся в файле user\_0.log. 

	В колонке **OutputDataset** щелкните срез, чтобы открыть колонку **Срез данных** для этого среза. Для этого среза будет указано **выполняемые действия**. Должно выполняться лишь одно действие. Если в командной строке нажать кнопку «Запуск», можно запустить другое действие для этого среза.

	Если щелкнуть выполняемое действие, появится колонка **Сведения о выполняемых действиях** со списком файлов журнала. Записанные в журнал сообщения можно увидеть в файле user\_0.log. Если возникнет ошибка, действие будет выполнено три раза, так как для количества повторных попыток в JSON-файле конвейера или действия задано значение 3. Если щелкнуть выполняемое действие, отобразятся файлы журнала, которые могут быть полезны для устранения ошибки.

	В списке файлов журнала щелкните **user-0.log**. На панели справа отображаются результаты использования метода **IActivityLogger.Write**.

	Следует также проверить файл **system-0.log** на наличие любых системных сообщений об ошибках или исключений.

3.	Добавьте **PDB**-файл в ZIP-файл, чтобы в случае возникновения ошибки сведения об ошибке содержали, например, информацию о **стеке вызовов**.
4.	Все файлы в ZIP-файле для пользовательского действия должны находиться на **верхнем уровне** без вложенных папок.
5.	Убедитесь, что для параметров **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) и **packageLinkedService** (должен указывать на хранилище BLOB-объектов Azure, содержащее ZIP-файл) установлены правильные значения. 
6.	Если ошибки устранены и необходимо повторно обработать срез, щелкните правой кнопкой мыши срез в колонке **OutputDataset** и нажмите кнопку **Запуск**. 


## Обновление пользовательского действия
Если вы обновляете код для настраиваемого действия, создайте его и отправьте ZIP-файл, содержащий новые двоичные файлы, в службу хранилища больших двоичных объектов.

## Доступ к расширенным свойствам
Вы можете объявить расширенные свойства в действии JSON, как показано ниже.

	"typeProperties": {
	  "AssemblyName": "MyDotNetActivity.dll",
	  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	  "PackageLinkedService": "StorageLinkedService",
	  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	  "extendedProperties": {
	    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
		"DataFactoryName": "CustomActivityFactory"
	  }
	},

В приведенном выше примере имеются два расширенных свойства: **SliceStart** и **DataFactoryName**. Значение свойства SliceStart основано на системной переменной SliceStart. Список поддерживаемых системных переменных см. в разделе [Системные переменные](data-factory-scheduling-and-execution.md#data-factory-system-variables). Значение свойства DataFactoryName жестко задано как CustomActivityFactory.

Для доступа к этим расширенным свойствам в методе **Execute** используйте код, аналогичный приведенному ниже:

	// to get extended properties (for example: SliceStart)
	DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
	string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

	// to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
    	logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
	}


## <a name="AzureBatch"></a>Использование связанной пакетной службы Azure
> [AZURE.NOTE]В статье [Основные сведения о пакетной службе Azure][batch-technical-overview] представлен обзор пакетной службы Azure, а статья [Начало работы с библиотекой Пакетной службы Azure для .NET][batch-get-started] поможет быстро начать работу с пакетной службой Azure.

Можно выполнить пользовательские действия .NET с помощью пакетной службы Azure в виде вычислительного ресурса. Необходимо создать пулы пакетной службы Azure и указать число виртуальных машин, а также другие конфигурации. Пулы пакетной службы Azure предоставляют следующие возможности для клиентов.

1. Создание пулов, содержащих от одного до нескольких тысяч ядер.
2. Автоматическое масштабирование числа виртуальных машин по формуле
3. Поддержка виртуальных машин любого размера
4. Возможность настройки количества задач для виртуальной машины
5. Неограниченное количество задач в очереди


Ниже приведены общие шаги по использованию Пакетной службы Azure в пошаговом руководстве, описанном в предыдущем разделе.

1. На [портале Azure](http://manage.windowsazure.com) создайте учетную запись пакетной службы Azure. Инструкции см. в статье [Создание учетной записи пакетной службы Azure и управление ею][batch-create-account]. Запишите ключ и имя учетной записи Пакетной службы Azure.

	Для создания учетной записи Пакетной службы Azure можно также воспользоваться командлетом [New-AzureBatchAccount][new-azure-batch-account]. Подробные инструкции по использованию этого командлета см. в записи блога [Использование Azure PowerShell для управления учетной записью Пакетной службы Azure][azure-batch-blog].
2. Создайте пул Пакетной службы Azure. Для этого можно скачать исходный код [инструмента Azure Batch Explorer][batch-explorer], выполнить компиляцию и использовать его или воспользоваться [библиотекой пакетной службы Azure для .NET][batch-net-library]. Пошаговые инструкции по использованию обозревателя Пакетной службы Azure приведены в записи блога [Пошаговое руководство по обозревателю Пакетной службы Azure][batch-explorer-walkthrough].

	Для создания пула пакетной службы Azure можно также воспользоваться командлетом [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx).

	Чтобы обрабатывать срезы параллельно, можно создать пул пакетной службы Azure по крайней мере с 2 вычислительными узлами. Если используется обозреватель пакетной службы:

	- Введите идентификатор для пула (**идентификатор пула**). Сохраните **идентификатор пула**. Он понадобится при создании решения фабрики данных. 
	- В качестве параметра семейства операционных систем укажите **Windows Server 2012 R2**.
	- Для параметра **Максимальное количество задач на вычислительный узел** укажите значение **2**.
	- Для параметра **Количество целевых выделенных объектов** укажите значение **2**. 

	В пакетной службе Azure служба фабрики данных создает задание с именем: adf-<pool name>:job-xxx. Такое задание создается для каждого запуска действия среза. При наличии 10 срезов, готовых к обработке, в рамках этого задания создаются 10 задач. Если в пуле доступны несколько вычислительных узлов, можно обрабатывать параллельно сразу несколько срезов. Если для максимального количества задач на вычислительный узел установлено значение > 1, также можно выполнять сразу несколько срезов в одной среде выполнения приложений.
	
	![Задачи обозревателя пакетной службы](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

	![Фабрика данных и пакетная служба](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

2. Создайте связанную Пакетную службу Azure с помощью следующего шаблона JSON. В редакторе фабрики данных отображается аналогичный шаблон, который можно использовать для начала работы. Укажите во фрагменте JSON имя и ключ учетной записи Пакетной службы Azure, а также имя пула для Пакетной службы Azure.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT]**URL-адрес** из **колонки учетной записи пакетной службы Azure** имеет следующий формат: accountname.region.batch.azure.com. Для свойства **batchUri** в JSON-файле необходимо **удалить элемент accountname.** из URL-адреса и использовать имя учетной записи **accountname** для свойства JSON **accountName**.

	Для свойства **poolName** можно также указать идентификатор пула вместо его имени.

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
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
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
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=AcomDC_0107_2016-->