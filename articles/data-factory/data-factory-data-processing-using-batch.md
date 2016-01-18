<properties 
    pageTitle="Обработка больших объемов данных с помощью фабрики данных Azure и пакетной службы Azure" 
    description="Содержит описание процесса обработки больших объемов данных в конвейере фабрики данных Azure с использованием возможности параллельной обработки пакетной службы Azure." 
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
    ms.date="12/16/2015" 
    ms.author="spelluru"/>
# Обработка больших объемов данных с помощью фабрики данных Azure и пакетной службы Azure

Это простое архитектурное решение показывает, как эффективно перемещать и обрабатывать крупномасштабные наборы данных в облаке с помощью **фабрики данных** **Microsoft Azure** и **пакетной службы** **Azure**. Эта архитектура подходит для многих сценариев, предполагающих обработку больших объемов данных. К таким сценариям относятся формирование отчетов и моделирование рисков в организациях по предоставлению финансовых услуг, обработка и отрисовка изображений, а также геномный анализ.

Архитекторы и руководители ИТ-отделов могут получить общие сведения, ознакомившись со схемой и основными шагами. Разработчики могут использовать код в качестве отправной точки для реализации своих решений. Эта статья содержит полное решение.

## Фабрика данных и пакетная служба

**Фабрика данных Azure** представляет собой облачную службу интеграции данных. Она координирует и автоматизирует перемещение необработанных данных и преобразует их в готовую к применению информацию. Чтобы освоить эту службу, см. статью [Введение в службу фабрики данных Azure](data-factory-introduction.md) и раздел [Создание первого конвейера](data-factory-build-your-first-pipeline.md).

Конвейеры представляют собой логические группы действий, предназначенные для перемещения и обработки данных. Фабрика данных поддерживает встроенные действия, такие как **действие копирования** и **действие Hive HDInsight**. Полный список действий см. в статьях [Действия перемещения данных](data-factory-data-movement-activities.md) и [Действия преобразования данных](data-factory-data-transformation-activities.md). Кроме того, можно создать **настраиваемое действие** на основе собственной логики обработки. Это будет показано в решении.

**Пакетная служба Azure** позволяет эффективно запускать приложения для крупномасштабных параллельных и высокопроизводительных вычислений (HPC) в облаке. Это служба платформы, которая планирует запуск ресурсоемких задач в управляемой коллекции виртуальных машин (вычислительных узлов) и может масштабировать вычислительные ресурсы в соответствии с требованиями задания. Дополнительные сведения см. в статьях [Основные сведения о пакетной службе Azure](../batch/batch-technical-overview.md) и [Обзор функций пакетной службы Azure](../batch/batch-api-basics.md).

## Схема архитектуры

На схеме показано, как фабрика данных управляет перемещением и обработкой данных и как пакетная служба Azure параллельно обрабатывает данные. Для удобства скачайте и распечатайте схему (приблизительно 28 x 43 см. или лист размером A3): [Пакетная служба Microsoft Azure и фабрика данных Azure — архитектура для обработки больших объемов данных](http://go.microsoft.com/fwlink/?LinkId=717686).

![](./media/data-factory-data-processing-using-batch/image1.png)

Ниже приведены основные этапы процесса. Решение содержит код и указания по созданию комплексного решения.

1.  Настройка пула вычислительных узлов (виртуальные машины) для пакетной службы Azure. Можно указать количество узлов и размер каждого из них.

2.  Создание экземпляра фабрики данных Azure, для которого настраиваются сущности, представляющие хранилище BLOB-объектов, службу вычислений пакетной службы Azure, входные и выходные данные, а также рабочий процесс и конвейер с действиями, позволяющими перемещать и преобразовывать данные.

3.  Конвейер фабрики данных содержит настраиваемое действие .NET, которое настраивается для запуска в пуле узлов пакетной службы Azure.

4.  Сохранение больших объемов входных данных в виде больших двоичных объектов в службе хранилища Azure. Данные при этом разделяются на логические срезы (обычно по времени).

5.  Фабрика данных копирует данные, которые будут параллельно обрабатываться, в дополнительное расположение.

6.  Фабрика данных запускает настраиваемое действие с помощью пула, выделенного пакетной службой. Действия в фабрике данных могут выполняться параллельно. В каждом действии обрабатывается один срез данных. Результаты сохраняются в службе хранилища Azure.

7.  После получения всех результатов фабрика данных перемещает результаты в третье расположение для распространения с помощью приложения или дальнейшей обработки с помощью других средств.

## Решение архитектуры

Решение вычисляет количество вхождений условия поиска (Microsoft) во входных файлах, упорядоченных по временным рядам. Количество выводится в выходные файлы.

**Время**. Если вы умеете пользоваться Azure, фабрикой данных и пакетной службой и выполнили предварительные требования, создание решения должно занять 1–2 часа.

### Предварительные требования

1.  **Подписка Azure**. Если у вас нет подписки Azure, всего за несколько минут можно создать учетную запись бесплатной пробной версии. См. сведения о [бесплатной пробной версии](http://azure.microsoft.com/pricing/free-trial/).

2.  **Учетная запись хранения Azure**. Учетная запись хранения Azure будет использоваться для хранения данных в этом учебнике. Если у вас нет учетной записи хранения Azure, см. раздел [Создание учетной записи хранения](../storage/storage-create-storage-account.md#create-a-storage-account). В примере решения используется хранилище BLOB-объектов.

3.  На [портале Azure](http://manage.windowsazure.com/) создайте **учетную запись пакетной службы Azure**. См. статью [Создание учетной записи пакетной службы Azure и управление ею](../batch/batch-account-create-portal.md). Запишите ключ и имя учетной записи пакетной службы Azure. Для создания учетной записи пакетной службы Azure можно также воспользоваться командлетом [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx). Подробные инструкции по использованию этого командлета см. в статье [Приступая к работе с командлетами PowerShell пакетной службы Azure](../batch/batch-powershell-cmdlets-get-started.md).

    В примере решения используется пакетная служба Azure (косвенно через конвейер фабрики данных Azure) для параллельной обработки данных в пуле вычислительных узлов, который является управляемой коллекцией виртуальных машин.

4.  Создайте **пул пакетной службы Azure** с как минимум 2 вычислительными узлами.

	 Для этого можно скачать исходный код [обозревателя пакетной службы Azure](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer), скомпилировать и использовать его (**настоятельно рекомендуется для этого примера решения**) для создания пула или воспользоваться [библиотекой пакетной службы Azure для .NET](../batch/batch-dotnet-get-started.md) для создания пула. Пошаговые инструкции по использованию обозревателя Пакетной службы Azure приведены в записи блога [Пошаговое руководство по обозревателю Пакетной службы Azure](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx). Для создания пула пакетной службы Azure можно также воспользоваться командлетом [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx).
	
	 Создайте пул с помощью обозревателя пакетной службы и укажите для него следующие параметры:

	-   Введите идентификатор для пула (**идентификатор пула**). Сохраните **идентификатор пула**. Он понадобится при создании решения фабрики данных.
	
	-   В качестве параметра **Семейство операционных систем** укажите **Windows Server 2012 R2**.
	
	-   Для параметра **Максимальное количество задач на вычислительный узел** укажите значение **2**.
	
	-   Для параметра **Количество целевых выделенных объектов** укажите значение **2**.
	
	 ![](./media/data-factory-data-processing-using-batch/image2.png)

5.  [Обозреватель хранилищ Azure версии 6 (средство)](https://azurestorageexplorer.codeplex.com/) или [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (от ClumsyLeaf Software). Это средства графического интерфейса пользователя, предназначенные для проверки и изменения данных в проектах службы хранилища Azure, включая журналы облачных приложений.

    1.  Создайте контейнер **mycontainer** с закрытым доступом (без анонимного доступа).

    2.  При использовании **CloudXplorer** создавайте папки и подпапки со следующей структурой:

 		![](./media/data-factory-data-processing-using-batch/image3.png)

		 **inputfolder** и **outputfolder** — папки верхнего уровня в контейнере **mycontainer**. Папка **inputfolder** содержит подпапки с метками даты и времени (ГГГГ-ММ-ДД-ЧЧ).
		
		 При использовании **обозревателя хранилищ Azure** на следующем шаге необходимо отправить файлы с именами inputfolder/2015-11-16-00/file.txt, inputfolder/2015-11-16-01/file.txt и т. д. Таким образом папки будут созданы автоматически.

	3.  Создайте на компьютере текстовый файл **file.txt** с содержимым, в котором есть ключевое слово **Microsoft**. Например, test custom activity Microsoft test custom activity Microsoft.

	4.  Отправьте файл в следующие входные папки хранилища BLOB-объектов Azure.

		![](./media/data-factory-data-processing-using-batch/image4.png)

	 	При использовании **обозревателя хранилищ Azure** отправьте файл **file.txt** в контейнер **mycontainer**. На панели инструментов щелкните **Копировать**, чтобы создать копию большого двоичного объекта. В диалоговом окне **Копирование большого двоичного объекта** измените **имя целевого большого двоичного объекта** на **inputfolder/2015-11-16-00/file.txt**. Повторите этот шаг, чтобы создать inputfolder/2015-11-16-01/file.txt, inputfolder/2015-11-16-02/file.txt, inputfolder/2015-11-16-03/file.txt, inputfolder/2015-11-16-04/file.txt и т. д. Таким образом папки будут созданы автоматически.

	3.  Создайте другой контейнер с именем **customactivitycontainer**. В этот контейнер будет добавлен ZIP-файл настраиваемого действия.

6.  **Microsoft Visual Studio 2012 или более поздней версии** (для создания настраиваемого действия пакетной службы, которое будет использоваться в решении фабрики данных).

### Пошаговое создание решения

1.  Создайте настраиваемое действие для использования в решении фабрики данных. Это действие содержит логику обработки данных.

    1.  В Visual Studio (или редакторе кода) создайте проект библиотеки классов .NET, добавьте код для обработки входных данных и скомпилируйте проект.

    2.  Запакуйте все двоичные файлы и PDB-файл (необязательно) в ZIP-архив в выходной папке.

    3.  Отправьте ZIP-файл в хранилище BLOB-объектов Azure.

	Подробные указания см. в разделе [Создание настраиваемого действия](#_Coding_the_custom).

2.  Создайте фабрику данных Azure, которая использует настраиваемое действие:

    1.  Создание фабрики данных Azure.

    2.  создание связанных служб.

        1.  StorageLinkedService — предоставляет учетные данные хранилища для доступа к большим двоичным объектам.

        2.  AzureBatchLinkedService — указывает пакетную службу Azure в качестве вычислительного ресурса.

    3.  Создайте наборы данных.

        1.  InputDataset — определяет контейнер хранилища и папку для входных больших двоичных объектов.

        2.  OuputDataset — определяет контейнер хранилища и папку для выходных больших двоичных объектов.

    4.  Создайте конвейер, который использует пользовательское действие.

    5.  Запустите и протестируйте конвейер.

    6.  Выполните отладку конвейера.

 	Подробные указания см. в разделе [Создание фабрики данных](#create-the-data-factory).

## Создание пользовательского действия

Настраиваемое действие фабрики данных составляет основу этого примера решения. Для выполнения этого действия в решении используется пакетная служба Azure. Общие сведения о разработке настраиваемых действий и их использовании в конвейере фабрики данных Azure см. в статье [Использование настраиваемых действий в конвейере фабрики данных Azure](data-factory-use-custom-activities.md).

Чтобы создать настраиваемое действие .NET, которое можно использовать в конвейере фабрики данных Azure, необходимо создать проект **библиотеки классов .NET** с классом, который реализует интерфейс **IDotNetActivity**. У этого интерфейса только один метод — **Execute**. Подпись метода выглядит следующим образом:

	public IDictionary<string, string> Execute(
	            IEnumerable<LinkedService> linkedServices, 
	            IEnumerable<Dataset> datasets, 
	            Activity activity, 
	            IActivityLogger logger)        

У этого метода есть несколько ключевых компонентов, с которыми нужно разобраться.

-   Метод принимает четыре параметра:

    1.  **linkedServices** — это перечисляемый список связанных служб, которые связывают источники входных и выходных данных (например, хранилище BLOB-объектов Azure) с фабрикой данных. В этом примере присутствует только одна связанная служба (служба хранилища Azure), которая используется для входных и выходных данных.

    2.  **datasets** — это перечисляемый список наборов данных. Этот параметр можно использовать для получения расположений и схем, которые определяются входными и выходными наборами данных.

    3.  **activity** — этот параметр представляет текущую вычислительную сущность (в этом примере — пакетную службу Azure).

    4.  **logger** — средство ведения журнала позволяет записывать комментарии отладки, которые отобразятся в виде пользовательского журнала для конвейера.

-   Этот метод возвращает словарь, который можно использовать для создания цепочки из пользовательских действий. В этом примере решения мы не будем использовать эту функцию.

### Процедура. Создание настраиваемого действия 

1.  Создайте проект библиотеки классов .NET в Visual Studio:

    1.  Запустите **Visual Studio 2012**/**2013/2015**.

    2.  Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**.

    3.  Разверните раздел **Шаблоны** и выберите **Visual C#**. В этом руководстве используется язык C#, но для создания настраиваемого действия вы можете использовать любой язык .NET.;

    4.  выберите тип **Библиотека классов** в списке типов проектов справа;

    5.  в поле **Имя** введите **MyDotNetActivity**;

    6.  В поле **Расположение** выберите **C:\\ADF**. Создайте папку **ADF**, если она не существует.

    7.  Нажмите кнопку **ОК**, чтобы создать проект.

2.  Щелкните **Инструменты**, наведите указатель мыши на **Диспетчер пакетов NuGet** и щелкните **Консоль диспетчера пакетов**.

3.  В **консоли диспетчера пакетов** выполните следующую команду, чтобы импортировать пакет **Microsoft.Azure.Management.DataFactories**.

			Install-Package Microsoft.Azure.Management.DataFactories

4.  Импортируйте пакет NuGet для **службы хранилища Azure** в проект. Он понадобится вам, так как будет использоваться API хранилища BLOB-объектов.

		Install-Package Azure.Storage

5.  Добавьте следующие директивы с **using** в исходный файл в проекте.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;
		
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;
		
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6.  Измените имя для параметра **namespace** на **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7.  Измените имя для класса на **MyDotNetActivity** и извлеките класс из интерфейса **IDotNetActivity**, как показано ниже.

		public class MyDotNetActivity : IDotNetActivity

8.  Реализуйте (добавьте) метод **Execute** интерфейса **IDotNetActivity** к классу **MyDotNetActivity** и скопируйте следующий пример кода в метод. Объяснение логики, используемой в этом методе, см. в разделе [Метод Execute](#execute-method).

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


9.  Добавьте в класс следующие вспомогательные методы. Их вызывает метод **Execute**. Самое главное, метод **Calculate** изолирует код, который выполняет итерацию каждого большого двоичного объекта.

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


	Метод **GetFolderPath** возвращает путь к папке, на которую указывает набор данных, а метод **GetFileName** — имя большого двоичного объекта или файла, на который указывает набор данных.

	    "name": "InputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "file.txt",
	            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",


	Метод **Calculate** вычисляет количество экземпляров ключевого слова **Microsoft** во входных файлах (в больших двоичных объектах в папке). Условие поиска (Microsoft) жестко запрограммировано в коде.

10.  Скомпилируйте проект. В меню щелкните **Построить** и **Построить решение**.

11.  Запустите **проводник Windows** и перейдите к папке **bin\\debug** или **bin\\release** в зависимости от типа сборки.

12.  Создайте ZIP-файл **MyDotNetActivity.zip**, который содержит все двоичные файлы из папки **\\bin\\Debug**. Можно также добавить файл **MyDotNetActivity.pdb**, чтобы в случае сбоя получить дополнительные сведения, например номер строки в исходном коде, вызвавшей ошибку.

	![](./media/data-factory-data-processing-using-batch/image5.png)

13.  Отправьте архив **MyDotNetActivity.zip** в качестве большого двоичного объекта в контейнер больших двоичных объектов **customactvitycontainer** из хранилища BLOB-объектов Azure, используемого связанной службой **StorageLinkedService** в **ADFTutorialDataFactory**. Создайте контейнер больших двоичных объектов **customactivitycontainer**, если он еще не создан.

### Метод Execute

В этом разделе содержатся дополнительные сведения и примечания о коде в методе Execute.

1.	Элементы для выполнения итерации входной коллекции находятся в пространстве имен [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). Для итерации коллекции больших двоичных объектов необходимо использовать класс **BlobContinuationToken**. В сущности, необходимо использовать цикл do-while и маркер в качестве механизма для выхода из цикла. Дополнительные сведения см. в статье [Использование хранилища BLOB-объектов из .NET](../storage/storage-dotnet-how-to-use-blobs.md). Базовый цикл выглядит так:

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

7.	Имя файла записано, и теперь можно записать выводимую строку из метода **Calculate** в новый большой двоичный объект:

		// Create a new blob and upload the output text.
		CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
		logger.Write("Writing {0} to the output blob", output);
		outputBlob.UploadText(output);


## Создание фабрики данных

В разделе [Создание настраиваемого действия](#create-the-custom-activity) вы создали настраиваемое действие и отправили ZIP-файл с двоичными файлами и PDB-файлом в контейнер больших двоичных объектов Azure. В этом разделе вы создадите **фабрику данных** Azure с **конвейером**, который использует **настраиваемое действие**.

Входной набор данных для пользовательского действия представляет собой большие двоичные объекты (файлы) во входной папке (mycontainer\\inputfolder) в хранилище BLOB-объектов. Выходной набор данных для пользовательского действия представляет собой выходные большие двоичные объекты в выходной папке (mycontainer\\outputfolder) в хранилище BLOB-объектов.

Во входные папки будут перенесены один или несколько файлов:

	mycontainer -> inputfolder
		2015-11-16-00
		2015-11-16-01
		2015-11-16-02
		2015-11-16-03
		2015-11-16-04

Например, перенесите один файл (file.txt) со следующим содержимым в каждую папку.

	test custom activity Microsoft test custom activity Microsoft

Каждая входная папка соответствует одному срезу в фабрике данных Azure, даже если она содержит 2 файла или более. При обработке каждого среза конвейером пользовательское действие выполняет итерацию всех больших двоичных объектов во входной папке для этого среза.

Появятся пять выходных файлов с одинаковым содержимым. Например, выходной файл, полученный в ходе обработки файла в папке 2015-11-16-00, будет включать следующее содержимое:

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

Если во входную папку перенести несколько файлов (file.txt, file2.txt, file3.txt) с одинаковым содержимым, в выходном файле появится следующее содержимое. В этом примере каждая папка (2015-11-16-00 и т. д.) соответствует одному срезу, несмотря на то, что она содержит несколько входных файлов.

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.


Обратите внимание, что теперь во входном файле появилось три строки, по одной на каждый входной файл (большой двоичный объект) в папке, связанный со срезом (2015-11-16-00).

Такая задача создается для каждого запуска действия. В этом примере в конвейере есть только одно действие. При обработке среза конвейером в пакетной службе Azure выполняется настраиваемое действие для обработки среза. Так как у нас есть 5 срезов (каждому срезу может соответствовать несколько больших двоичных объектов или файлов), в пакетной службе Azure будет создано 5 задач. При запуске задачи в пакетной службе фактически выполняется настраиваемое действие.

В следующем пошаговом руководстве содержатся дополнительные сведения.

### Шаг 1. Создание фабрики данных

1.  Войдите на [портал Azure](http://portal.azure.com/) и сделайте следующее:

    1.  В меню слева нажмите кнопку **СОЗДАТЬ**.

    2.  В колонке **Создание** щелкните **Данные и аналитика**.

    3.  В колонке **Аналитика данных** щелкните **Фабрика данных**.

2.  В колонке **Создание фабрики данных** в поле «Имя» введите **CustomActivityFactory**. Имя фабрики данных Azure должно быть глобально уникальным. Если возникнет ошибка **Имя фабрики данных CustomActivityFactory недоступно**, измените имя этой фабрики данных (например, на **вашеимяCustomActivityFactory**) и попробуйте создать ее снова.

3.  Щелкните **ИМЯ ГРУППЫ РЕСУРСОВ**, чтобы выбрать существующую группу ресурсов, или создайте группу ресурсов.

4.  Убедитесь, что используется именно та подписка и регион, в которых необходимо создать фабрику данных.

5.  В колонке **Создание фабрики данных** щелкните **Создать**.

6.  Созданная фабрика данных появится на **панели мониторинга** портала Azure.

7.  Ее содержимое отображается на специальной странице.

 ![](./media/data-factory-data-processing-using-batch/image6.png)

### Шаг 2. Создание связанных служб

Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. На этом этапе учетная запись **службы хранилища Azure** и учетная запись **пакетной службы Azure** будут связаны с фабрикой данных.

#### Создание связанной службы хранения Azure

1.  В колонке **ФАБРИКА ДАННЫХ** для **CustomActivityFactory** щелкните элемент **Создание и развертывание**. Откроется редактор фабрики данных.

2.  На панели команд щелкните **Создание хранилища данных** и выберите **Служба хранилища Azure**. В редакторе отобразится сценарий JSON для создания связанной службы хранилища Azure.

    ![](./media/data-factory-data-processing-using-batch/image7.png)

3.  Замените **account name** именем своей учетной записи хранения Azure, а **account key** — ключом доступа к учетной записи хранения Azure. Сведения о получении ключа доступа к хранилищу см. в разделах о [просмотре, копировании и повторном создании ключей доступа к хранилищу](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

4.  Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

    ![](./media/data-factory-data-processing-using-batch/image8.png)

#### Создание связанной пакетной службы Azure

На этом шаге вы создадите связанную службу для учетной записи **пакетной службы Azure**, в которой будет выполняться настраиваемое действие фабрики данных.

1.  В командной строке щелкните **Создать вычисление** и выберите **Пакетная служба Azure**. В редакторе отобразится сценарий JSON для создания связанной пакетной службы Azure.

2.  В сценарии JSON выполните следующее:

    1.  Замените **account name** именем учетной записи пакетной службы Azure.

    2.  Замените **access key** ключом доступа к учетной записи пакетной службы Azure.

    3.  Введите идентификатор пула для свойства **poolName****.** Для него можно задать имя или идентификатор пула.

    4.  Введите URI пакета для свойства JSON **batchUri**. **URL-адрес** из **колонки учетной записи пакетной службы Azure** имеет следующий формат: <имя\_учетной\_записи>.<регион>.batch.azure.com. В свойстве **batchUri** в JSON-файле потребуется **удалить «имя\_учетной\_записи»** в URL-адресе. Например, batchUri: https://eastus.batch.azure.com.

        ![](./media/data-factory-data-processing-using-batch/image9.png)

    5.  Укажите **StorageLinkedService** в свойстве **linkedServiceName**. Эта связанная служба была создана на предыдущем шаге. Это хранилище используется в качестве промежуточной области для файлов и журналов.

3.  Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

### Шаг 3. Создание наборов данных

На этом шаге вы создадите наборы данных, которые представляют входные и выходные данные.

#### Создание входного набора данных

1.  В **Редакторе** фабрики данных нажмите на панели инструментов кнопку **Создать набор данных** и выберите в раскрывающемся меню пункт **Хранилище больших двоичных объектов Azure**.

2.  Замените код JSON в правой области на следующий фрагмент кода JSON:

		{
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
		            "format": {
		                "type": "TextFormat"
		            },
		            "partitionedBy": [
		                {
		                    "name": "Year",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy"
		                    }
		                },
		                {
		                    "name": "Month",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "MM"
		                    }
		                },
		                {
		                    "name": "Day",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "dd"
		                    }
		                },
		                {
		                    "name": "Hour",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "HH"
		                    }
		                }
		            ]
		        },
		        "availability": {
		            "frequency": "Hour",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	
	 Позже в этом пошаговом руководстве вы создадите конвейер со временем начала 2015-11-16T00:00:00Z и временем окончания 2015-11-16T05:00:00Z. Данные будут создаваться **почасово**, поэтому мы получим 5 входных и выходных срезов (в промежутке **00**:00:00 -> **05**:00:00).
	
	 Для параметров **frequency** и **interval** входного набора данных установлены значения **Hour** и **1**. Это означает, что входной срез данных будет создаваться каждый час.
	
	 Это значения времени начала для каждого среза, представленные системной переменной **SliceStart** в приведенном выше фрагменте кода JSON.
	
	| **Срез** | **Время начала** |
	|-----------|-------------------------|
	| 1 | 2015-11-16T**00**:00:00 |
	| 2 | 2015-11-16T**01**:00:00 |
	| 3 | 2015-11-16T**02**:00:00 |
	| 4 | 2015-11-16T**03**:00:00 |
	| 5 | 2015-11-16T**04**:00:00 |
	
	 Значение параметра **folderPath** вычисляется с использованием года, месяца, дня и часа значения времени начала среза (**SliceStart**). Именно так входная папка сопоставляется со срезом.
	
	| **Срез** | **Время начала** | **Входная папка** |
	|-----------|-------------------------|-------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3.  На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть таблицу **InputDataset**. Убедитесь, что на панели заголовка редактора отображается сообщение **ТАБЛИЦА УСПЕШНО СОЗДАНА**.

#### Создание выходного набора данных

На этом шаге вы создадите еще один набор данных типа AzureBlob для представления выходных данных.

1.  В **Редакторе** фабрики данных нажмите на панели инструментов кнопку **Создать набор данных** и выберите в раскрывающемся меню пункт **Хранилище больших двоичных объектов Azure**.

2.  Замените код JSON в правой области на следующий фрагмент кода JSON:

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "mycontainer/outputfolder",
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


 	Для каждого входного среза данных создается выходной большой двоичный объект или файл. Ниже в таблице приведены имена, которые даются выходным файлам для каждого среза. Все выходные файлы создаются в одной выходной папке: **mycontainer\\outputfolder**.


	| **Срез** | **Время начала** | **Выходной файл** |
	|-----------|-------------------------|-----------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |
	
	 Помните, что все файлы во входной папке (например, 2015-11-16-00) являются частью среза со значениями времени начала (2015-11-16-00). Во время обработки этого среза пользовательское действие сканирует каждый файл и создает строку в выходном файле с количеством вхождений условия поиска (Microsoft). Если в папке 2015-11-16-00 есть три файла, в выходном файле 2015-11-16-00.txt будет три строки.

3.  На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть **OutputDataset**.

### Шаг 4. Создание и запуск конвейера с настраиваемым действием

На этом шаге вы создадите конвейер с одним настраиваемым действием, созданным ранее.

> [AZURE.IMPORTANT]Если файл **file.txt** не отправлен во входные папки в контейнере больших двоичных объектов, сделайте это, прежде чем создавать конвейер. В JSON-файле конвейера для свойства **IsPaused** задано значение false, поэтому конвейер запустится сразу по прошествии даты, указанной в параметре **start**.

1.  В редакторе фабрики данных, нажмите кнопку **Создать конвейер** на панели команд. Если команда не отображается, нажмите кнопку **... (многоточие)**, чтобы отобразить ее.

2.  Замените сценарий JSON в правой области на следующий:

		{
			"name": "PipelineCustom",
			"properties": {
				"description": "Use custom activity",
				"activities": [
					{
						"type": "DotNetActivity",
						"typeProperties": {
							"assemblyName": "MyDotNetActivity.dll",
							"entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
							"packageLinkedService": "StorageLinkedService",
							"packageFile": "customactivitycontainer/MyDotNetActivity.zip"
						},
						"inputs": [
							{
								"name": "InputDataset"
							}
						],
						"outputs": [
							{
								"name": "OutputDataset"
							}
						],
						"policy": {
							"timeout": "00:30:00",
							"concurrency": 5,
							"retry": 3
						},
						"scheduler": {
							"frequency": "Hour",
							"interval": 1
						},
						"name": "MyDotNetActivity",
						"linkedServiceName": "AzureBatchLinkedService"
					}
				],
				"start": "2015-11-16T00:00:00Z",
				"end": "2015-11-16T05:00:00Z",
				"isPaused": false
		   }
		}

	Обратите внимание на следующее.

	-   В конвейере содержится одно действие типа **DotNetActivity**.

	-   Для **AssemblyName** установлено имя библиотеки DLL **MyDotNetActivity.dll**.

	-   для **EntryPoint** — **MyDotNetActivityNS.MyDotNetActivity**; По сути, этот параметр представляет фрагмент кода <пространство\_имен>.<имя\_класса>.

	-   Для параметра **PackageLinkedService** задано значение **StorageLinkedService**, которое указывает на хранилище BLOB-объектов, содержащее ZIP-файл настраиваемого действия. Если для входных и выходных файлов и ZIP-файла пользовательского действия используются разные учетные записи службы хранилища Azure, необходимо создать другую связанную службу хранилища Azure. В этой статье предполагается использование одной и той же учетной записи хранения Azure.

	-   Для **PackageFile** установлено значение **customactivitycontainer/MyDotNetActivity.zip**. Используется формат <контейнер\_для\_zip-файла>/<имя\_zip-файла.zip>.

	-   Настраиваемое действие принимает **InputDataset** в качестве входных данных и **OutputDataset** — в качестве выходных данных.

	-   Свойство **linkedServiceName** настраиваемого действия указывает на **AzureBatchLinkedService**, которое сообщает фабрике данных Azure, что необходимо запустить настраиваемое действие в пакетной службе Azure.

	-   Обратите внимание на параметр **concurrency**. Если используется значение по умолчанию, т. е. 1, то даже если в пуле пакетной службы Azure есть 2 или несколько вычислительных узлов, срезы обрабатываются по очереди. Таким образом игнорируется возможность параллельной обработки пакетной службы Azure. Если задать для параметра **concurrency** большее значение, например 2, это означает, что одновременно могут обрабатываться 2 среза (соответствующие 2 задачам в пакетной службе Azure). В этом случае используются обе виртуальные машины в пуле пакетной службы Azure. Исходя из этого, укажите соответствующее значение для свойства concurrency.

	-   По умолчанию на виртуальной машине в любой момент времени будет выполняться только одна задача (один срез). Так происходит, потому что по умолчанию для параметра **Максимальное количество задач на виртуальную машину** задано значение 1 для пула пакетной службы Azure. В ходе выполнения необходимых требований создан пул и для этого свойства задано значение 2. Поэтому на виртуальной машине могут выполняться два среза фабрики данных одновременно.


	-   По умолчанию для свойства **isPaused** установлено значение false. В этом примере конвейер запускается незамедлительно, потому что срезы приходятся на прошлое. Для этого свойства можно задать значение true, чтобы приостановить работу конвейера. Чтобы перезапустить его, нужно снова установить значение false.

	-   Разница между временем начала (**start**) и временем окончания (**end**) составляет 5 часов, а срезы создаются каждый час. Таким образом, конвейер создает 5 срезов.

3.  Чтобы развернуть конвейер, нажмите кнопку **Развернуть** на панели команд.

### Шаг 5. Тестирование конвейера

На этом шаге вы протестируете конвейер. Для этого файлы будут перенесены во входные папки. Сначала перенесем один файл в одну входную папку.

1.  На портале Azure в колонке «Фабрика данных» щелкните **Схема**.

    ![](./media/data-factory-data-processing-using-batch/image10.png)

2.  В представлении схемы дважды щелкните входной набор данных **InputDataset**.

    ![](./media/data-factory-data-processing-using-batch/image11.png)

3.  Должна появиться колонка **InputDataset** со всеми 5 срезами. Обратите внимание на значения в столбцах **ВРЕМЯ НАЧАЛА СРЕЗА** и **ВРЕМЯ ОКОНЧАНИЯ СРЕЗА** для каждого среза.

    ![](./media/data-factory-data-processing-using-batch/image12.png)

4.  Теперь в **представлении схемы** щелкните **OutputDataset**.

5.  Если срезы уже созданы, вы увидите 5 выходных срезов данных в состоянии готовности.

    ![](./media/data-factory-data-processing-using-batch/image13.png)

6.  В [обозревателе пакетной службы Azure](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx) просмотрите **задачи**, связанные со **срезами**, а также сведения о том, на какой виртуальной машине выполнялся каждый срез. Задание создается с именем **adf-<имя\_пула>**. Это задание будет содержать по одной задаче для каждого среза. В этом примере будет использоваться 5 срезов, соответствующих 5 задачам в пакетной службе Azure. Так как для свойства **concurrency** задано значение **5** в JSON-файле конвейера в фабрике данных Azure, а для параметра **Максимальное количество задач на виртуальную машину** — **2** в пуле пакетной службы Azure с **2** виртуальными машинами, задачи выполнялись очень быстро (см. значение времени в столбце **Создано**).

    ![](./media/data-factory-data-processing-using-batch/image14.png)

7.  Выходные файлы должны появиться в папке **outputfolder** контейнера **mycontainer** в хранилище BLOB-объектов Azure.

    ![](./media/data-factory-data-processing-using-batch/image15.png)

    Должно появиться пять выходных файлов, по одному на каждый входной срез. В каждом из выходных файлов должно быть содержимое, аналогичное приведенному ниже:

    	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

    На следующей схеме показано сопоставление срезов данных фабрики и задач в пакетной службе Azure. В этом примере срез запускался всего один раз.

    ![](./media/data-factory-data-processing-using-batch/image16.png)

8.  Теперь давайте попробуем использовать несколько файлов в папке. Создайте файлы **file2.txt**, **file3.txt**, **file4.txt** и **file5.txt** с тем же содержимым, что и в файле file.txt в папке **2015-11-06-01**.

9.  В выходной папке **удалите** выходной файл **2015-11-16-01.txt**.

10. Теперь в колонке **OutputDataset** щелкните правой кнопкой мыши срез, для которого в столбце **ВРЕМЯ НАЧАЛА СРЕЗА** задано значение **11/16/2015 01:00:00 AM**, и выберите пункт **Запустить** для повторного выполнения и повторной обработки среза. Теперь срез содержит 5 файлов, а не 1 файл.

    ![](./media/data-factory-data-processing-using-batch/image17.png)

11. Когда после запуска среза его состояние изменится на **Готово**, проверьте содержимое в выходном файле для этого среза (**2015-11-16-01.txt**) в папке **outputfolder** из контейнера **mycontainer** в хранилище BLOB-объектов. В выходном файле каждому файлу среза должна соответствовать одна строка.

		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.


    **Примечание.** Если выходной файл 2015-11-16-01.txt не удален перед использованием 5 входных файлов, появится одна строка из предыдущего запуска среза и пять строк из текущего запуска среза. По умолчанию содержимое добавляется в выходной файл, если он уже существует.

### Выполнение отладки конвейера

Отладка состоит из нескольких базовых методов:

1.  Если для входного среза данных не установлено значение **Готово**, убедитесь, что структура входной папки правильная и что в ней существует файл file.txt.

    ![](./media/data-factory-data-processing-using-batch/image3.png)

2.  В методе **Execute** настраиваемого действия используйте объект **IActivityLogger**, чтобы записывать в журнал сведения, которые помогут устранить неполадки. Сообщения, записанные в журнал, отобразятся в файле user\_0.log.

    В колонке **OutputDataset** щелкните срез, чтобы открыть колонку **Срез данных** для этого среза. Для этого среза будет указано **выполняемые действия**. Должно выполняться лишь одно действие. Если в командной строке щелкнуть **Запуск**, можно запустить другое действие для этого среза.

    Если щелкнуть выполняемое действие, появится колонка **СВЕДЕНИЯ О ВЫПОЛНЯЕМЫХ ДЕЙСТВИЯХ** со списком файлов журнала. Записанные в журнал сообщения можно увидеть в файле **user\_0.log**. Если возникнет ошибка, действие будет выполнено три раза, так как для количества повторных попыток в JSON-файле конвейера или действия задано значение 3. Если щелкнуть выполняемое действие, отобразятся файлы журнала, которые могут быть полезны для устранения ошибки.

    ![](./media/data-factory-data-processing-using-batch/image18.png)

    В списке файлов журнала щелкните **user-0.log**. На панели справа отображаются результаты использования метода **IActivityLogger.Write**.

    ![](./media/data-factory-data-processing-using-batch/image19.png)

    Следует также проверить файл system-0.log на наличие любых системных сообщений об ошибках или исключений.

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

		Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully

3.  Добавьте **PDB-файл** в ZIP-файл, чтобы в случае возникновения ошибки сведения об ошибке содержали, например, информацию о **стеке вызовов**.

4.  Все файлы в ZIP-файле для пользовательского действия должны находиться на **верхнем уровне** без вложенных папок.

    ![](./media/data-factory-data-processing-using-batch/image20.png)

5.  Убедитесь, что для параметров **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) и **packageLinkedService** (должен указывать на хранилище BLOB-объектов Azure, содержащее ZIP-файл) установлены правильные значения.

6.  Если ошибки устранены и необходимо повторно обработать срез, щелкните правой кнопкой мыши срез в колонке **OutputDataset** и выберите пункт **Запустить**.

    ![](./media/data-factory-data-processing-using-batch/image21.png)

    **Примечание.** В хранилище BLOB-объектов Azure появится **контейнер** с именем **adfjobs**. Этот контейнер не удаляется автоматически, но его можно безопасно удалить после завершения тестирования решения. Аналогичным образом решение фабрики данных создает **задание** пакетной службы Azure с именем **adf-<идентификатор\_и\_имя\_пула>:job-0000000001**. При необходимости после выполнения тестирования решения это задание можно удалить.

### Расширение примера

Для получения дополнительных сведений о функциях фабрики данных Azure и пакетной службы Azure этот пример можно расширить. Например, чтобы обрабатывать срезы с использованием другого диапазона времени, выполните следующее:

1.  Добавьте в папку **inputfolder** подпапки 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08, 2015-11-16-09 и поместите в них входные файлы. Измените время окончания для конвейера с 2015-11-16T05:00:00Z на 2015-11-16T10:00:00Z. В **представлении схемы** дважды щелкните **InputDataset** и убедитесь, что для входных срезов отображается состояние «Готово». Дважды щелкните **OuptutDataset**, чтобы просмотреть состояние выходных срезов. Если отображается состояние «Готово», проверьте папку outputfolder для выходных файлов.

2.  Укажите для параметра **concurrency** большее или меньшее значение, чтобы понять, как это влияет на производительность решения, особенно на обработку в пакетной службе Azure. (Дополнительные сведения о параметре **concurrency** см. в разделе «Шаг 4. Создание и запуск конвейера».)

3.  Создайте пул с высоким или низким значением для параметра **Максимальное количество задач на виртуальную машину**. Обновите связанную пакетную службу Azure в решении фабрики данных, чтобы воспользоваться созданным пулом. (Дополнительные сведения о параметре **Максимальное количество задач на виртуальную машину** см. в разделе «Шаг 4. Создание и запуск конвейера».)

4.  Создайте пул пакетной службы Azure с использованием функции **автомасштабирования**. Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure представляет собой динамическую настройку вычислительной мощности, используемой вашим приложением. Дополнительные сведения см. в статье [Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure](../batch/batch-automatic-scaling.md).

    В примере решения метод **Execute** вызывает метод **Calculate**, который обрабатывает входной срез данных для создания выходного среза данных. Можно написать собственный метод для обработки входных данных и заменить вызов метода Calculate в методе Execute его вызовом.

## Дальнейшие действия. Использование данных

После обработки данных их можно использовать в интерактивных средствах, например в **Microsoft Power BI**. Ниже приведены ссылки на статьи, в которых представлены общие сведения о Power BI и информация о том, как использовать это средство в Azure.

-   [Изучение набора данных в Power BI](https://support.powerbi.com/knowledgebase/articles/475159)

-   [Начало работы с Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/471664)

-   [Обновление данных в Power BI](https://support.powerbi.com/knowledgebase/articles/474669)

-   [Общая информация об Azure и Power BI](https://support.powerbi.com/knowledgebase/articles/568614)

## Ссылки

-   [Фабрика данных Azure](https://azure.microsoft.com/documentation/services/data-factory/)

    -   [Введение в службу фабрики данных Azure](data-factory-introduction.md)

    -   [Начало работы с фабрикой данных Azure](data-factory-build-your-first-pipeline.md)

    -   [Использование настраиваемых действий в конвейере фабрики данных Azure](data-factory-use-custom-activities.md)

-   [Пакетная служба Azure](https://azure.microsoft.com/documentation/services/batch/)

    -   [Основные сведения о пакетной службе Azure](../batch/batch-technical-overview.md)

    -   [Обзор функций пакетной службы Azure](../batch/batch-api-basics.md)

    -   [Создание учетной записи пакетной службы Azure на портале Azure и управление ею](../batch/batch-account-create-portal.md)

    -   [Начало работы с библиотекой Пакетной службы Azure для .NET](../batch/batch-dotnet-get-started.md)

<!---HONumber=AcomDC_0107_2016-->