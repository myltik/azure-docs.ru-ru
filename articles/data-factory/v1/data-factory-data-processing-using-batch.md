---
title: "Обработка больших наборов данных с использованием фабрики данных и пакетной службы | Документация Майкрософт"
description: "Содержит описание процесса обработки больших объемов данных в конвейере фабрики данных Azure с использованием возможности параллельной обработки пакетной службы Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 75213a4d0297c96ec32200158d8b60db4b8b2da4
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Обработка больших наборов данных с помощью фабрики данных и пакетной службы
В этой статье описывается архитектура простого решения, которое автоматически и по расписанию перемещает и обрабатывает большие наборы данных. Здесь также предоставлено полное пошаговое руководство по реализации решения с помощью фабрики данных Azure и пакетной службы Azure.

Эта статья длиннее обычной статьи, так как в ней содержится пошаговое руководство для всего примера решения. Если вы впервые используете пакетную службу и фабрику данных, здесь вы можете узнать об этих службах и их совместном использовании. Если вы немного знакомы с этими службами и проектируете решение, то можете сосредоточиться только на [разделе об архитектуре](#architecture-of-sample-solution). Если вы разрабатываете прототип или решение, то можете попытаться выполнить пошаговые инструкции из приведенного [руководства](#implementation-of-sample-solution). Мы приветствуем ваши комментарии об этой статье и использовании ее содержимого.

Сначала давайте рассмотрим, как фабрика данных и пакетная служба могут быть полезны для обработки больших наборов данных в облаке.     

## <a name="why-azure-batch"></a>Сведения о пакетной службе Azure
Пакетная служба Azure позволяет эффективно запускать приложения, выполняющие крупномасштабные параллельные вычислительные задания с высокой производительностью, в облаке. Это служба платформы, которая планирует запуск ресурсоемких вычислительных задач в управляемой коллекции виртуальных машин и автоматически масштабирует вычислительные ресурсы в соответствии с требованиями задания.

С помощью пакетной службы вы можете определять вычислительные ресурсы Azure, необходимые для выполнения приложений в параллельном режиме и в нужном масштабе. Вы можете выполнять задания по запросу или по расписанию. При этом вам не нужно вручную создавать и настраивать кластер высокопроизводительных вычислений (HPC), отдельные виртуальные машины, виртуальные сети или сложную инфраструктуру планировщика задач и заданий, а также управлять всеми этими ресурсами.

Если вы не знакомы с пакетной службой Azure, изучите приведенные ниже статьи. Это поможет вам понять архитектуру и схему реализации решения, описанного в этой статье.   

* [Основные сведения о пакетной службе Azure](../../batch/batch-technical-overview.md)
* [Обзор функций пакетной службы](../../batch/batch-api-basics.md)

Чтобы получить дополнительные сведения о пакетной службе Azure, ознакомьтесь со [схемой обучения по пакетной службе Azure](https://azure.microsoft.com/documentation/learning-paths/batch/) (необязательно).

## <a name="why-azure-data-factory"></a>Сведения о фабрике данных Azure
Фабрика данных представляет собой облачную службу интеграции информации, которая организует и автоматизирует перемещение и преобразование данных. Фабрика данных позволяет создавать управляемые конвейеры данных, которые перемещают данные из локальных и облачных хранилищ данных в централизованное хранилище (например, в хранилище BLOB-объектов Azure), а также обрабатывают и преобразовывают данные с помощью служб, таких как Azure HDInsight и Машинное обучение Azure. Вы можете планировать запуск конвейеров данных по расписанию (ежечасно, ежедневно, еженедельно и т. д.), отслеживать их состояние и управлять ими, чтобы обнаруживать проблемы и принимать меры.

Если вы не знакомы с фабрикой данных Azure, просмотрите приведенные ниже статьи. Это поможет понять архитектуру и реализацию решения, описанного в этой статье.  

* [Общие сведения о службе фабрики данных Azure](data-factory-introduction.md)
* [Построение конвейера данных](data-factory-build-your-first-pipeline.md)   

Чтобы получить дополнительные сведения о фабрике данных Azure, ознакомьтесь со [схемой обучения по фабрике данных Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/) (необязательно).

## <a name="data-factory-and-batch-together"></a>Совместное использование фабрики данных и пакетной службы
Фабрика данных включает в себя встроенные действия, например действие копирования, позволяющее копировать или перемещать данные из исходного хранилища данных в целевое, и действие HIVE, позволяющее обрабатывать данные с использованием кластеров Hadoop (HDInsight) в Azure. Список поддерживаемых действий преобразования см. в [этой статье](data-factory-data-transformation-activities.md).

Благодаря этой службе вы можете также создавать настраиваемые действия .NET для перемещения или обработки данных с помощью собственной логики, а также выполнять эти действия в кластере Azure HDInsight или в пуле виртуальных машин пакетной службы Azure. При использовании пакетной службы Azure можно настроить автоматическое масштабирование пула (добавление или удаление виртуальных машин в зависимости от рабочей нагрузки) по предоставленной формуле.     

## <a name="architecture-of-sample-solution"></a>Архитектура примера решения
Несмотря на то, что архитектура, описанная в этой статье, предназначена для простого решения, она подходит для таких сложных сценариев, как моделирование рисков по финансовым услугам, обработка и отрисовка изображений и геномный анализ.

На схеме показано, как фабрика данных управляет перемещением и обработкой данных и как пакетная служба Azure параллельно обрабатывает данные. Скачайте и распечатайте схему для удобства (в формате 297 x 420 мм или А3): [Управление данными и высокопроизводительными вычислениями с помощью пакетной службы и фабрики данных Azure](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Схема обработки данных большого объема](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Ниже приведен список основных этапов процесса. Решение содержит код и указания по созданию комплексного решения.

1. **Настройка пула вычислительных узлов (виртуальные машины) для пакетной службы Azure**. Можно указать количество узлов и размер каждого из них.
2. **Создание экземпляра фабрики данных Azure** , для которого настраиваются сущности, представляющие хранилище BLOB-объектов, службу вычислений пакетной службы Azure, входные и выходные данные, а также рабочий процесс и конвейер с действиями, позволяющими перемещать и преобразовывать данные.
3. **Создание настраиваемого действия .NET в конвейере фабрики данных**. Действие является пользовательским кодом, который выполняется в пуле пакетной службы Azure.
4. **Сохранение больших объемов входных данных в виде больших двоичных объектов в службе хранилища Azure**. Данные при этом разделяются на логические срезы (обычно по времени).
5. **Фабрика данных копирует данные, которые параллельно обрабатываются** и передаются в дополнительное расположение.
6. **Фабрика данных запускает настраиваемое действие с помощью пула, выделенного пакетной службой**. Действия в фабрике данных могут выполняться параллельно. В каждом действии обрабатывается один срез данных. Результаты сохраняются в службе хранилища Azure.
7. **Фабрика данных перемещает окончательные результаты в третье расположение**для распространения с помощью приложения или дальнейшей обработки с помощью других инструментов.

## <a name="implementation-of-sample-solution"></a>Реализация примера решения
Пример решения намеренно простой, чтобы продемонстрировать совместное использование фабрики данных и пакетной службы для обработки наборов данных. Решение просто вычисляет количество вхождений условия поиска (Microsoft) во входных файлах, упорядоченных по временным рядам. Количество выводится в выходные файлы.

**Время**. Если вы знакомы с основами использования Azure, фабрики данных и пакетной службы, а также выполнили предварительные требования, перечисленные ниже, создание решения должно занять 1–2 часа.

### <a name="prerequisites"></a>Предварительные требования
#### <a name="azure-subscription"></a>Подписка Azure.
Если у вас нет подписки Azure, то всего за несколько минут можно создать бесплатную пробную учетную запись. Узнайте больше о [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Учетная запись хранения Azure.
Учетная запись хранения Azure используется в этом учебнике для хранения данных. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом [Создание учетной записи хранения](../../storage/common/storage-create-storage-account.md#create-a-storage-account). В примере решения используется хранилище BLOB-объектов.

#### <a name="azure-batch-account"></a>Учетная запись пакетной службы Azure
Создание учетной записи пакетной службы Azure на [портале Azure](http://manage.windowsazure.com/). Ознакомьтесь со статьей [Создание учетной записи пакетной службы Azure и управление ею](../../batch/batch-account-create-portal.md). Запишите ключ и имя учетной записи пакетной службы Azure. Для создания учетной записи пакетной службы Azure можно также воспользоваться командлетом [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx). Подробные инструкции по использованию этого командлета см. в статье [Приступая к работе с командлетами PowerShell пакетной службы Azure](../../batch/batch-powershell-cmdlets-get-started.md).

В примере решения используется пакетная служба Azure (косвенно, через конвейер фабрики данных Azure) для параллельной обработки данных в пуле вычислительных узлов (управляемой коллекции виртуальных машин).

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>Пул виртуальных машин пакетной службы Azure
Создайте **пул пакетной службы Azure** с как минимум 2 вычислительными узлами.

1. На [портале Azure](https://portal.azure.com) щелкните **Обзор** в меню слева и выберите **Уч. записи пакетной службы**.
2. Выберите свою учетную запись пакетной службы Azure, чтобы открыть колонку **Учетная запись пакетной службы** .
3. Щелкните элемент **Пулы** .
4. В колонке **Пулы** нажмите кнопку "Добавить" на панели инструментов, чтобы добавить пул.
   1. Введите идентификатор для пула (**идентификатор пула**). Сохраните **идентификатор пула**. Он понадобится при создании решения фабрики данных.
   2. В качестве параметра семейства операционных систем укажите **Windows Server 2012 R2** .
   3. Выберите **Ценовая категория для узлов**.
   4. Для параметра **Выделенный целевой объект** укажите значение **2**.
   5. Для параметра **Максимальное число заданий на узел** укажите значение **2**.
   6. Нажмите кнопку **ОК** , чтобы создать пул.

#### <a name="azure-storage-explorer"></a>Обозреватель хранилищ Azure
[Azure Storage Explorer 6 (инструмент)](https://azurestorageexplorer.codeplex.com/) или [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (от ClumsyLeaf Software). Эти инструменты предназначены для проверки и изменения данных в проектах службы хранилища Azure, включая журналы облачных приложений.

1. Создайте контейнер **mycontainer** с закрытым доступом (без анонимного доступа).
2. При использовании **CloudXplorer**создавайте папки и подпапки со следующей структурой:

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`и `outputfolder` — это папки верхнего уровня в `mycontainer`. `inputfolder` содержит вложенные папки с метками даты и времени (ГГГГ-ММ-ДД-ЧЧ).

   При использовании **Azure Storage Explorer** на следующем шаге необходимо передать файлы с именами `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` и т. д. На этом шаге автоматически будут созданы папки.
3. Создайте на компьютере текстовый файл **file.txt** с содержимым, в котором есть ключевое слово **Microsoft**. Например, test custom activity Microsoft test custom activity Microsoft.
4. Отправьте файл в следующие входные папки хранилища BLOB-объектов Azure.

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   При использовании **Azure Storage Explorer** передайте файл **file.txt** в контейнер **mycontainer**. На панели инструментов щелкните **Копировать**, чтобы создать копию большого двоичного объекта. В диалоговом окне **Копирование большого двоичного объекта** измените **имя целевого большого двоичного объекта** на `inputfolder/2015-11-16-00/file.txt`. Повторите этот шаг, чтобы создать `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` и т. д. Это действие автоматически создает данные папки.
5. Создайте другой контейнер с именем: `customactivitycontainer`. В этот контейнер передается ZIP-файл настраиваемого действия.

#### <a name="visual-studio"></a>Visual Studio
Установите Microsoft Visual Studio 2012 или более поздней версии, чтобы создать настраиваемое действие пакетной службы, которое будет использоваться в решении фабрики данных.

### <a name="high-level-steps-to-create-the-solution"></a>Пошаговое создание решения
1. Создайте настраиваемое действие, содержащее логику обработки данных.
2. Создайте фабрику данных Azure, которая использует настраиваемое действие:

### <a name="create-the-custom-activity"></a>Создание пользовательского действия
Настраиваемое действие фабрики данных составляет основу этого примера решения. Для выполнения этого действия в решении используется пакетная служба Azure. Общие сведения о разработке настраиваемых действий и их использовании в конвейере фабрики данных Azure см. в статье [Использование настраиваемых действий в конвейере фабрики данных Azure](data-factory-use-custom-activities.md).

Чтобы создать настраиваемое действие .NET, которое можно использовать в конвейере фабрики данных Azure, необходимо создать проект **библиотеки классов .NET** с классом, который реализует интерфейс **IDotNetActivity**. У этого интерфейса только один метод — **Execute**. Подпись метода выглядит следующим образом:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

У этого метода есть несколько ключевых компонентов, с которыми нужно разобраться.

* Метод принимает четыре параметра:

  1. **linkedServices** — это перечисляемый список связанных служб, которые связывают источники входных и выходных данных (например, хранилище BLOB-объектов Azure) с фабрикой данных. В этом примере присутствует только одна связанная служба (служба хранилища Azure), которая используется для входных и выходных данных.
  2. **наборы данных**. это перечисляемый список наборов данных. Этот параметр можно использовать для получения расположений и схем, которые определяются входными и выходными наборами данных.
  3. **activity** — этот параметр представляет текущую вычислительную сущность (в этом примере — пакетную службу Azure).
  4. **logger** — средство ведения журнала позволяет записывать комментарии отладки, которые отображаются в виде пользовательского журнала для конвейера.
* Этот метод возвращает словарь, который можно будет использовать для создания цепочки из настраиваемых действий в будущем. Эта функция еще не реализована, поэтому из метода просто возвращается пустой словарь.

#### <a name="procedure-create-the-custom-activity"></a>Процедура. Создание настраиваемого действия
1. Создайте проект библиотеки классов .NET в Visual Studio:

   1. Запустите **Visual Studio 2012,** /**Visual Studio 2013 или Visual Studio 2015**.
   2. Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**.
   3. Разверните раздел **Шаблоны** и выберите **Visual C#\#**. В этом руководстве используется язык C\#, но для создания настраиваемого действия вы можете использовать любой язык .NET.
   4. Выберите тип **Библиотека классов** из списка типов проектов справа.
   5. В поле **Имя** введите **MyDotNetActivity**.
   6. В поле **Расположение\\ выберите** C:**ADF**. Создайте папку **ADF** , если она не существует.
   7. Нажмите кнопку **ОК** , чтобы создать проект.
2. Щелкните **Инструменты**, наведите указатель мыши на **Диспетчер пакетов NuGet** и щелкните **Консоль диспетчера пакетов**.
3. В **консоли диспетчера пакетов** выполните следующую команду, чтобы импортировать пакет **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Импортируйте пакет NuGet для **службы хранилища Azure** в проект. Он нужен, так как в данном примере используется API хранилища BLOB-объектов.

    ```powershell
    Install-Package Azure.Storage
    ```
5. Добавьте следующие директивы с **using** в исходный файл в проекте.

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Измените имя **пространства имен** на **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Измените имя класса на **MyDotNetActivity** и извлеките класс из интерфейса **IDotNetActivity**, как показано ниже.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Реализуйте (добавьте) метод **Execute** интерфейса **IDotNetActivity** для класса **MyDotNetActivity** и скопируйте следующий пример кода в этот метод. Объяснение логики, используемой в этом методе, см. в разделе [Метод Execute](#execute-method).

    ```csharp
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
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
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
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
9. Добавьте в класс следующие вспомогательные методы. Их вызывает метод **Execute** . Самое главное, метод **Calculate** изолирует код, который выполняет итерацию каждого большого двоичного объекта.

    ```csharp
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
    /// and prepares the output text that is written to the output blob.
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
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Метод **GetFolderPath** возвращает путь к папке, на которую указывает набор данных, а метод **GetFileName** — имя большого двоичного объекта или файла, на который указывает набор данных.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Метод **Calculate** вычисляет количество экземпляров ключевого слова **Microsoft** во входных файлах (в больших двоичных объектах в папке). Условие поиска (Microsoft) жестко задано в коде.

1. Скомпилируйте проект. В меню щелкните **Построить** и **Построить решение**.
2. Запустите **проводник Windows** и перейдите к папке **bin\\debug** или **bin\\release** в зависимости от типа сборки.
3. Создайте ZIP-файл **MyDotNetActivity.zip**, который содержит все двоичные файлы из папки **\\bin\\Debug**. Можно также добавить файл MyDotNetActivity.**pdb**, чтобы в случае сбоя получить дополнительные сведения, например номер строки в исходном коде, вызвавшей ошибку.

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. Передайте архив **MyDotNetActivity.zip** в качестве большого двоичного объекта в контейнер больших двоичных объектов `customactivitycontainer` в хранилище BLOB-объектов Azure (используется связанной службой **StorageLinkedService** в **ADFTutorialDataFactor**). Если контейнер `customactivitycontainer` еще не существует, создайте его.

#### <a name="execute-method"></a>Метод Execute
В этом разделе содержатся дополнительные сведения и примечания о коде в методе Execute.

1. Элементы для выполнения итерации входной коллекции находятся в пространстве имен [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). Для итерации коллекции больших двоичных объектов необходимо использовать класс **BlobContinuationToken**. В сущности, необходимо использовать цикл do-while и маркер в качестве механизма для выхода из цикла. Дополнительные сведения см. в статье [Использование хранилища BLOB-объектов из .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Базовый цикл выглядит так:

    ```csharp
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

    ```
   Дополнительные сведения см. в документации по методу [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx).
2. Код для работы с набором больших двоичных объектов логически находится в цикле do-while. В методе **Execute** цикл do-while передает список больших двоичных объектов в метод **Calculate**. Этот метод возвращает строковую переменную с именем **output** , которая является результатом итерации всех больших двоичных объектов в сегменте.

   Он возвращает число вхождений условия поиска (**Microsoft**) в большом двоичном объекте, переданном в метод **Calculate**.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. После завершения работы метода **Calculate** его результаты записываются в новый большой двоичный объект. Следовательно, для каждого обработанного набора больших двоичных объектов можно записать новый большой двоичный объект с результатами. Чтобы записать новый большой двоичный объект, сначала найдите выходной набор данных.

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. Код также вызывает вспомогательный метод **GetFolderPath** , чтобы получить путь к папке (имя контейнера хранилища).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Метод **GetFolderPath** приводит объект DataSet к AzureBlobDataSet со свойством FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. Код вызывает метод **GetFileName** , чтобы получить имя файла (имя большого двоичного объекта). Используемый код аналогичен приведенному выше коду, используемому для получения пути к папке.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Чтобы записать имя файла, создается объект универсального кода ресурса (URI). Для возврата имени контейнера в конструкторе URI используется свойство **BlobEndpoint** . Для создания URI выходного большого двоичного объекта сочетаются путь к папке и имя файла.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Имя файла записано, и теперь можно записать выводимую строку из метода **Calculate** в новый большой двоичный объект:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Создание фабрики данных
В разделе [Создание настраиваемого действия](#create-the-custom-activity) вы создали настраиваемое действие и отправили ZIP-файл с двоичными файлами и PDB-файлом в контейнер больших двоичных объектов Azure. В этом разделе вы создадите **фабрику данных** Azure с **конвейером**, использующим **настраиваемое действие**.

Входной набор данных для пользовательского действия представляет собой большие двоичные объекты (файлы) во входной папке (`mycontainer\\inputfolder`) в хранилище BLOB-объектов. Выходной набор данных для пользовательского действия представляет собой выходные большие двоичные объекты в папке выходных данных (`mycontainer\\outputfolder`) в хранилище BLOB-объектов.

Перенесите в папки входных данных один или несколько файлов.

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Например, перенесите один файл (file.txt) со следующим содержимым в каждую папку.

```
test custom activity Microsoft test custom activity Microsoft
```

Каждая входная папка соответствует одному срезу в фабрике данных Azure, даже если она содержит 2 файла или более. При обработке каждого среза конвейером пользовательское действие выполняет итерацию всех больших двоичных объектов во входной папке для этого среза.

Отображаются пять выходных файлов с одинаковым содержимым. Например, выходной файл, полученный в ходе обработки файла в папке 2015-11-16-00, включает в себя следующее содержимое.

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Если в папку входных данных перенести несколько файлов (file.txt, file2.txt, file3.txt) с одинаковым содержимым, то в выходном файле будет следующее содержимое. В этом примере каждая папка (2015-11-16-00 и т. д.) соответствует одному срезу, несмотря на то, что она содержит несколько входных файлов.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Теперь во входном файле появилось три строки, по одной на каждый входной файл (большой двоичный объект) в папке, связанный со срезом (2015-11-16-00).

Такая задача создается для каждого запуска действия. В этом примере в конвейере есть только одно действие. При обработке среза конвейером в пакетной службе Azure выполняется настраиваемое действие для обработки среза. Так как имеется 5 срезов (каждому срезу может соответствовать несколько больших двоичных объектов или файлов), в пакетной службе Azure создано 5 задач. При запуске задачи в пакетной службе фактически выполняется настраиваемое действие.

В следующем пошаговом руководстве содержатся дополнительные сведения.

#### <a name="step-1-create-the-data-factory"></a>Шаг 1. Создание фабрики данных
1. Войдите на [портал Azure](https://portal.azure.com/) и сделайте следующее.

   1. В меню слева нажмите кнопку **Создать** .
   2. В колонке **Создание** щелкните **Данные и аналитика**.
   3. Щелкните **Фабрика данных** в колонке **Аналитика данных**.
2. В колонке **Создание фабрики данных** в поле "Имя" введите **CustomActivityFactory**. Имя фабрики данных Azure должно быть глобально уникальным. Если возникнет ошибка **Имя фабрики данных CustomActivityFactory недоступно**, измените имя этой фабрики данных (например, на **ваше_имяCustomActivityFactory**) и попробуйте создать ее снова.
3. Щелкните **Имя группы ресурсов**, чтобы выбрать имеющуюся группу ресурсов, или создайте группу ресурсов.
4. Убедитесь, что используется именно та подписка и регион, в которых необходимо создать фабрику данных.
5. В колонке **Создание фабрики данных** нажмите кнопку **Создать**.
6. Созданная фабрика данных появится на **панели мониторинга** портала Azure.
7. Ее содержимое отобразится на соответствующей странице.

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Шаг 2. Создание связанных служб
Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. На этом этапе учетная запись **хранения Azure** и учетная запись **пакетной службы Azure** будут связаны с фабрикой данных.

#### <a name="create-azure-storage-linked-service"></a>Создание связанной службы хранения Azure
1. В колонке **Фабрика данных** для **CustomActivityFactory** щелкните элемент **Создание и развертывание**. Отобразится редактор фабрики данных.
2. На панели команд щелкните **Создание хранилища данных** и выберите **Служба хранилища Azure**. В редакторе отобразится сценарий JSON для создания связанной службы хранилища Azure.

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. Замените **account name** именем своей учетной записи хранения Azure, а **account key** — ключом доступа к ней. Сведения о получении ключа доступа к хранилищу см. в разделах о [просмотре, копировании и повторном создании ключей доступа к хранилищу](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Создание связанной пакетной службы Azure
На этом шаге вы создаете связанную службу для учетной записи **пакетной службы Azure**, в которой будет выполняться настраиваемое действие фабрики данных.

1. Щелкните **Новое вычисление** в командной строке и выберите **Пакетная служба Azure**. В редакторе отобразится сценарий JSON для создания связанной пакетной службы Azure.
2. В сценарии JSON выполните следующее:

   1. Замените **account name** именем учетной записи пакетной службы Azure.
   2. Замените **access key** ключом доступа к учетной записи пакетной службы Azure.
   3. Введите идентификатор пула для свойства **poolName** **.** Для этого свойства можно задать имя пула или идентификатор пула.
   4. Введите URI пакета для свойства JSON **batchUri** .

      > [!IMPORTANT]
      > **URL-адрес** из **колонки учетной записи пакетной службы Azure** имеет следующий формат: \<имя_учетной_записи\>.\<регион\>.batch.azure.com. В свойстве **batchUri** в JSON требуется **удалить заполнитель accountname** в URL-адресе. Пример: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      Для свойства **poolName** можно также указать идентификатор пула вместо его имени.

      > [!NOTE]
      > Служба фабрики данных не поддерживает параметр по требованию для пакетной службы Azure, как для HDInsight. Пул пакетной службы Azure можно использовать только в фабрике данных Azure.
      >
      >
   5. В качестве параметра семейства операционных систем укажите **StorageLinkedService** for the **linkedServiceName** . Эта связанная служба была создана на предыдущем шаге. Это хранилище используется в качестве промежуточной области для файлов и журналов.
3. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

#### <a name="step-3-create-datasets"></a>Шаг 3. Создание наборов данных
На этом шаге вы создадите наборы данных, которые представляют входные и выходные данные.

#### <a name="create-input-dataset"></a>Создание входного набора данных
1. В **редакторе** фабрики данных нажмите на панели инструментов кнопку **Новый набор данных** и выберите в раскрывающемся меню пункт **Хранилище BLOB-объектов Azure**.
2. Замените код JSON в правой области на следующий фрагмент кода JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
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
    ```

    Позже в этом пошаговом руководстве вы создадите конвейер со временем начала 2015-11-16T00:00:00Z и временем окончания 2015-11-16T05:00:00Z. Данные будут создаваться **почасово**, поэтому мы получим 5 входных и выходных срезов (между **00**:00:00 -\> **05**:00:00).

    Для параметров **frequency** и **interval** входного набора данных установлены значения **Hour** и **1**. Это означает, что входной срез данных будет создаваться каждый час.

    Это значения времени начала для каждого среза, представленные системной переменной **SliceStart** в приведенном выше фрагменте кода JSON.

    | **Срез** | **Время начала**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4.         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    Значение параметра **folderPath** вычисляется с использованием года, месяца, дня и часа значения времени начала среза (**SliceStart**). Именно так входная папка сопоставляется со срезом.

    | **Срез** | **Время начала**          | **Входная папка**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4.         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть таблицу **InputDataset**.

#### <a name="create-output-dataset"></a>Создание выходного набора данных
На этом шаге создается еще один набор данных типа AzureBlob для представления выходных данных.

1. В **редакторе** фабрики данных нажмите на панели инструментов кнопку **Новый набор данных** и выберите в раскрывающемся меню пункт **Хранилище BLOB-объектов Azure**.
2. Замените код JSON в правой области на следующий фрагмент кода JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
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
    ```

    Для каждого входного среза данных создается выходной большой двоичный объект или файл. Ниже в таблице приведены имена, которые даются выходным файлам для каждого среза. Все выходные файлы создаются в одной папке выходных данных: `mycontainer\\outputfolder`.

    | **Срез** | **Время начала**          | **Выходной файл**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4.         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Помните, что все файлы во входной папке (например, 2015-11-16-00) являются частью среза со значениями времени начала (2015-11-16-00). Во время обработки этого среза пользовательское действие сканирует каждый файл и создает строку в выходном файле с количеством вхождений условия поиска (Microsoft). Если в папке 2015-11-16-00 есть три файла, то в выходном файле 2015-11-16-00.txt будет три строки.

1. На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>Шаг 4. Создание и запуск конвейера с настраиваемым действием
На этом шаге создается конвейер с одним настраиваемым действием, созданным ранее.

> [!IMPORTANT]
> Если файл **file.txt** не отправлен в папки входных данных в контейнере больших двоичных объектов, сделайте это, прежде чем создавать конвейер. В JSON конвейера для свойства **IsPaused** задано значение false, поэтому конвейер запустится сразу по прошествии даты, указанной в параметре **start**.
>
>

1. В редакторе фабрики данных, нажмите кнопку **Создать конвейер** на панели команд. Если команда не отображается, нажмите кнопку **... (многоточие)**, чтобы отобразить ее.
2. Замените сценарий JSON в правой области приведенным ниже.

    ```json
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
                       "packageLinkedService": "AzureStorageLinkedService",
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
    ```
   Обратите внимание на следующие моменты.

   * В конвейере содержится одно действие типа **DotNetActivity**.
   * В **AssemblyName** задано имя библиотеки DLL **MyDotNetActivity.dll**.
   * В **EntryPoint** — **MyDotNetActivityNS.MyDotNetActivity**. По сути, это фрагмент кода \<пространство_имен\>.\<имя_класса\>.
   * Для параметра **PackageLinkedService** задано значение **StorageLinkedService**, которое указывает на хранилище BLOB-объектов, содержащее ZIP-файл настраиваемого действия. Если для входных и выходных файлов и ZIP-файла настраиваемого действия используются разные учетные записи хранения Azure, то необходимо создать другую связанную службу хранилища Azure. В этой статье предполагается использование одной и той же учетной записи хранения Azure.
   * Для **PackageFile** установлено значение **customactivitycontainer/MyDotNetActivity.zip**. Используется формат \<контейнер_для_zip-файла\>/\<имя_zip-файла.zip\>.
   * Настраиваемое действие принимает **InputDataset** в качестве входных данных и **OutputDataset** — в качестве выходных данных.
   * Свойство **linkedServiceName** настраиваемого действия указывает на **AzureBatchLinkedService**, которое сообщает фабрике данных Azure, что необходимо запустить настраиваемое действие в пакетной службе Azure.
   * Обратите внимание на параметр **concurrency**. Если используется значение по умолчанию, т. е. 1, то даже если в пуле пакетной службы Azure есть 2 или несколько вычислительных узлов, срезы обрабатываются по очереди. Таким образом игнорируется возможность параллельной обработки пакетной службы Azure. Если задать для параметра **concurrency** большее значение, например 2, это означает, что одновременно могут обрабатываться два среза (соответствующие двум задачам в пакетной службе Azure). В этом случае используются обе виртуальные машины в пуле пакетной службы Azure. Исходя из этого, укажите соответствующее значение для свойства concurrency.
   * По умолчанию на виртуальной машине в любой момент времени будет выполняться только одна задача (один срез). Так происходит, потому что по умолчанию для параметра **Maximum tasks per VM** (Максимальное количество задач на виртуальную машину) задано значение 1 для пула пакетной службы Azure. В ходе выполнения необходимых требований создан пул и для этого свойства задано значение 2. Поэтому на виртуальной машине могут выполняться два среза фабрики данных одновременно.

    -   Для свойства **isPaused** по умолчанию установлено значение false. В этом примере конвейер запускается незамедлительно, потому что срезы приходятся на прошлое. Для этого свойства можно задать значение true, чтобы приостановить работу конвейера. Чтобы перезапустить его, нужно снова установить значение false.

    -   Разница между временем **начала** и временем **окончания** составляет пять часов, а срезы создаются каждый час. Таким образом конвейер создает пять срезов.

1. Чтобы развернуть конвейер, нажмите кнопку **Развернуть** на панели команд.

#### <a name="step-5-test-the-pipeline"></a>Шаг 5. Тестирование конвейера
На этом шаге вы протестируете конвейер. Для этого файлы будут перенесены в папки входных данных. Сначала перенесем один файл в одну входную папку.

1. На портале Azure в колонке "Фабрика данных" щелкните **Схема**.

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. В представлении схемы дважды щелкните входной набор данных **InputDataset**.

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. Должна появиться колонка **InputDataset** со всеми пятью срезами. Обратите внимание на значения в столбцах **Время начала среза** и **Время окончания среза** для каждого среза.

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. Теперь в **представлении схемы** щелкните **OutputDataset**.
5. Если срезы уже созданы, вы увидите пять выходных срезов данных в состоянии готовности.

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. На портале Azure просмотрите **задачи**, связанные со **срезами**, а также сведения о том, на какой виртуальной машине выполнялся каждый срез. Дополнительные сведения см. в разделе [Интеграция фабрики данных и пакетной службы](#data-factory-and-batch-integration).
7. Выходные файлы должны появиться в папке `outputfolder` контейнера `mycontainer` в хранилище BLOB-объектов Azure.

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   Должно появиться пять выходных файлов, по одному на каждый входной срез. В каждом из выходных файлов должно быть содержимое, аналогичное приведенному ниже.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   На следующей схеме показано сопоставление срезов данных фабрики и задач в пакетной службе Azure. В этом примере срез запускался всего один раз.

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. Теперь давайте попробуем использовать несколько файлов в папке. Создайте файлы **file2.txt**, **file3.txt**, **file4.txt** и **file5.txt** с тем же содержимым, что и в файле file.txt в папке **2015-11-06-01**.
9. В папке выходных данных **удалите** выходной файл **2015-11-16-01.txt**.
10. Теперь в колонке **OutputDataset** щелкните правой кнопкой мыши срез, для которого в столбце **Время начала среза** задано значение **11/16/2015 01:00:00 AM**, и выберите пункт **Запустить** для повторного выполнения и обработки среза. Теперь в срезе пять файлов вместо одного.

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. Когда после запуска среза его состояние изменится на **Готов**, проверьте содержимое в выходном файле для этого среза (**2015-11-16-01.txt**) в папке `outputfolder` контейнера `mycontainer` в хранилище BLOB-объектов. В выходном файле каждому файлу среза должна соответствовать одна строка.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Если не удалить выходной файл 2015-11-16-01.txt перед использованием пяти входных файлов, появится одна строка из предыдущего запуска среза и пять строк из текущего. По умолчанию содержимое добавляется в выходной файл, если он уже существует.
>
>

#### <a name="data-factory-and-batch-integration"></a>Интеграция фабрики данных и пакетной службы
Служба фабрики данных создает в пакетной службе Azure задание с именем `adf-poolname:job-xxx`.

![Фабрика данных Azure — задания пакетной службы](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Для каждого запуска действия среза в задании создается задача. При наличии 10 срезов, готовых к обработке, в рамках этого задания создаются 10 задач. Если в пуле доступны несколько вычислительных узлов, можно обрабатывать параллельно сразу несколько срезов. Если для максимального количества задач на вычислительный узел установлено значение > 1, можно также выполнять сразу несколько срезов в одном цикле вычислений.

В этом примере имеется пять срезов, что соответствует пяти задачам в пакетной службе Azure. Так как для свойства **concurrency** задано значение **5** в JSON конвейера в фабрике данных Azure, а для параметра **Maximum tasks per VM** (Максимальное число задач на виртуальную машину) — **2** в пуле пакетной службы Azure с **2** виртуальными машинами, задачи будут выполняться очень быстро (просмотрите время начала и завершения задач).

На портале просмотрите задание пакетной службы и его задачи, связанные со **срезами** , а также сведения о том, на какой виртуальной машине выполнялся каждый срез.

![Фабрика данных Azure — задачи задания пакетной службы](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Выполнение отладки конвейера
Отладка состоит из нескольких базовых методов:

1. Если для входного среза данных не установлено значение **Готово**, убедитесь, что структура входной папки правильная и что в ней существует файл file.txt.

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. В методе **Execute** настраиваемого действия используйте объект **IActivityLogger**, чтобы записывать в журнал сведения, которые помогут устранить неполадки. Сообщения, записанные в журнал, сохраняются в файле user\_0.log.

   В колонке **OutputDataset** щелкните срез, чтобы открыть для него колонку **Срез данных**. Для этого среза будет указано значение **Запуски операции** . Должно выполняться лишь одно действие. Если в командной строке щелкнуть **Запуск** , можно запустить другое действие для этого среза.

   Если щелкнуть выполняемое действие, появится колонка **Подробности о выполнении операции** со списком файлов журнала. Записанные в журнал сообщения можно просмотреть в файле **user\_0.log**. Если возникнет ошибка, действие будет выполнено три раза, так как для количества повторных попыток в JSON-файле конвейера или действия задано значение 3. Если щелкнуть выполняемое действие, отобразятся файлы журнала, которые можно использовать для устранения ошибки.

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   В списке файлов журнала щелкните **user-0.log**. На панели справа отображаются результаты использования метода **IActivityLogger.Write** .

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   Проверьте файл system-0.log на наличие любых системных сообщений об ошибках или исключений.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Добавьте **PDB-файл** в ZIP-файл, чтобы в случае возникновения ошибки сведения об ошибке содержали, например, информацию о **стеке вызовов**.
4. Все файлы в ZIP-файле для пользовательского действия должны находиться на **верхнем уровне** без вложенных папок.

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. Убедитесь, что для параметров **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) и **packageLinkedService** (должен указывать на хранилище BLOB-объектов Azure, содержащее ZIP-файл) установлены правильные значения.
6. Если ошибки устранены и необходимо повторно обработать срез, в колонке **OutputDataset** щелкните срез правой кнопкой мыши и выберите пункт **Запуск**.

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > В хранилище BLOB-объектов Azure появится **контейнер** с именем `adfjobs`. Этот контейнер не удаляется автоматически, но его можно безопасно удалить после завершения тестирования решения. Аналогичным образом решение фабрики данных создает **задание** пакетной службы Azure с именем `adf-\<pool ID/name\>:job-0000000001`. При необходимости после выполнения тестирования решения это задание можно удалить.
   >
   >
7. Настраиваемое действие не использует файл **app.config** из пакета. Поэтому, если код считывает какие-либо строки подключения из файла конфигурации, это не сработает во время выполнения. При использовании пакетной службы Azure рекомендуется хранить все секреты в **хранилище ключей Azure**. Кроме того, следует использовать субъект-службу на основе сертификата для защиты хранилища ключей и переместить сертификат в пул пакетной службы Azure. В этом случае пользовательское действие .NET в среде выполнения сможет использовать секреты из хранилища ключей. Это универсальное решение, которое можно использовать для любых типов секрета, а не только строк подключения.

    Существует и более простое (но не самое лучшее) решение: можно создать **связанную службу SQL Azure** с параметрами строки подключения, набор данных, использующий связанную службу, и привязать его как фиктивный набор входных данных к настраиваемому действию .NET. После этого вы сможете получить доступ к строке подключения связанной службы из кода пользовательского действия — в среде выполнения она должна работать нормально.  

#### <a name="extend-the-sample"></a>Расширение примера
Для получения дополнительных сведений о функциях фабрики данных Azure и пакетной службы Azure этот пример можно расширить. Например, чтобы обрабатывать срезы с использованием другого диапазона времени, выполните следующее.

1. Добавьте в папку `inputfolder` подпапки 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08, 2015-11-16-09 и поместите в них входные файлы. Измените время окончания для конвейера с `2015-11-16T05:00:00Z` на `2015-11-16T10:00:00Z`. В **представлении схемы** дважды щелкните **InputDataset** и убедитесь, что для входных срезов отображается состояние "Готово". Дважды щелкните **OuptutDataset** , чтобы просмотреть состояние выходных срезов. Если отображается состояние "Готово", проверьте выходные файлы в выходной папке.
2. Укажите для параметра **concurrency** большее или меньшее значение, чтобы понять, как это влияет на производительность решения, особенно на обработку в пакетной службе Azure. (Дополнительные сведения о параметре **concurrency** см. в разделе "Шаг 4. Создание и запуск конвейера".)
3. Создайте пул с большим или меньшим значением для параметра **Maximum tasks per VM** (Максимальное число задач на виртуальную машину). Чтобы воспользоваться созданным пулом, обновите связанную службу пакетной службы Azure в решении фабрики данных. (Дополнительные сведения о параметре **Maximum tasks per VM** (Максимальное число задач на виртуальную машину) см. в разделе "Шаг 4. Создание и запуск конвейера".)
4. Создайте пул пакетной службы Azure с использованием функции **автомасштабирования** . Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure представляет собой динамическую настройку вычислительной мощности, используемой вашим приложением. 

    Приведенный пример формулы обеспечивает следующее поведение: при создании пул изначально содержит одну виртуальную машину. Метрика $PendingTasks определяет количество задач в состоянии выполнения и активном состоянии (в очереди).  Формула находит среднее число ожидающих выполнения задач за последние 180 секунд и соответствующим образом задает значение TargetDedicated. Благодаря этому значение TargetDedicated никогда не превысит 25 виртуальных машин. Таким образом, по мере поступления новых задач пул автоматически увеличивается, а по мере их завершения виртуальные машины высвобождаются по одной, и функция автоматического масштабирования уменьшает пул. Значения startingNumberOfVMs и maxNumberofVMs можно настроить в соответствии со своими потребностями.
 
    Формула автоматического масштабирования:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Дополнительные сведения см. в статье [Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure](../../batch/batch-automatic-scaling.md).

   Если в пуле используется [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)(значение по умолчанию), пакетной службе может потребоваться 15–30 минут на подготовку виртуальной машины перед выполнением настраиваемого действия.  Если пул использует другое значение autoScaleEvaluationInterval, пакетная служба может затрачивать autoScaleEvaluationInterval плюс 10 минут.
5. В примере решения метод **Execute** вызывает метод **Calculate**, который обрабатывает входной срез данных для создания выходного среза данных. Можно написать собственный метод для обработки входных данных и заменить вызов метода Calculate в методе Execute его вызовом.

### <a name="next-steps-consume-the-data"></a>Дальнейшие действия. Использование данных
После обработки данных их можно использовать в интерактивных инструментах, например в **Microsoft Power BI**. Ниже приведены ссылки на статьи, в которых представлены общие сведения о Power BI и информация о том, как использовать это средство в Azure.

* [Изучение набора данных в Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Обновление данных в Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Общая информация об Azure и Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Ссылки
* [Фабрика данных Azure](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Введение в службу фабрики данных Azure](data-factory-introduction.md)
  * [Начало работы с фабрикой данных Azure](data-factory-build-your-first-pipeline.md)
  * [Использование настраиваемых действий в конвейере фабрики данных Azure](data-factory-use-custom-activities.md)
* [Пакетная служба Azure](https://azure.microsoft.com/documentation/services/batch/)

  * [Основные сведения о пакетной службе Azure](../../batch/batch-technical-overview.md)
  * [Обзор функций пакетной службы Azure](../../batch/batch-api-basics.md)
  * [Создание учетной записи пакетной службы Azure на портале Azure и управление ею](../../batch/batch-account-create-portal.md)
  * [Начало работы с библиотекой Пакетной службы Azure для .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

