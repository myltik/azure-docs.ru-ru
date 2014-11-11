<properties linkid="manage-services-storage-net-shared-access-signature-part-2" urlDisplayName="" pageTitle="Create and use a SAS with the Blob Service | Microsoft Azure" metaKeywords="Azure blob, shared access signatures, stored access policy" description="Explore generating and using shared access signatures with the Blob service" metaCanonical="" services="storage" documentationCenter="" title="Part 2: Create and Use a SAS with the Blob Service" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Подписи общего доступа, часть 2: Создание и использование подписи общего доступа с помощью службы BLOB-объектов

[В части 1][В части 1] этого учебника приведен обзор подписей общего доступа (SAS), а также даны советы и рекомендации по их использованию. В части 2 показано, как создать и затем использовать подписи общего доступа с помощью службы BLOB-объектов Azure. Примеры написаны на C# и используют библиотеку клиента хранения Azure для .NET. Рассмотренные здесь сценарии включают в себя следующие аспекты работы с подписями общего доступа:

-   Создание подписи общего доступа для контейнера
-   Создание подписи общего доступа для BLOB-объекта
-   Создание хранимой политики доступа для управления подписями в ресурсах контейнера
-   Проверка подписей общего доступа из клиентского приложения

# Сведения о данном учебнике

В этом учебнике мы сосредоточим внимание на создании подписей общего доступа для контейнеров и BLOB-объектов путем создания двух консольных приложений. Первое консольное приложение создает подписи общего доступа для контейнера и BLOB-объекта. Этому приложению известны ключи учетной записи хранения. Второе консольное приложение, которое будет выступать в качестве клиентского, обращается к ресурсам контейнера и BLOB-объекта с использованием подписей общего доступа, созданных с помощью первого приложения. Это приложение использует подписи общего доступа только для проверки подлинности доступа к ресурсам контейнера и BLOB-объекта и не располагает сведениями о ключах учетной записи.

# Часть 1: Создание консольного приложения для создания подписей общего доступа

Прежде всего убедитесь, что установлена библиотека клиента хранения Azure для .NET. Можно установить [пакет NuGet][пакет NuGet], содержащий самые новые сборки для клиентской библиотеки; этот способ рекомендуется использовать, чтобы обеспечить наличие самых актуальных исправлений. Можно также загрузить клиентскую библиотеку в составе последней версии [пакета Azure SDK для .NET][пакета Azure SDK для .NET].

В Visual Studio создайте новое консольное приложение Windows и назовите его **GenerateSharedAccessSignatures**. Добавьте ссылки на **Microsoft.WindowsAzure.Configuration.dll** и **Microsoft.WindowsAzure.Storage.dll**, используя один из следующих подходов:

-   Если нужно установить пакет NuGet, сначала установите [расширение диспетчера пакетов NuGet для Visual Studio][расширение диспетчера пакетов NuGet для Visual Studio]. В Visual Studio выберите **Проект | Управление пакетами NuGet**, выполните в Интернете поиск значения **Хранилище Azure** и следуйте инструкциям по установке.
-   Можно также найти нужные сборки в установленных файлах пакета SDK Azure и добавить ссылки на них.

Добавьте в начало файла Program.cs следующие инструкции **using**:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Измените файл app.config, содержащий параметр конфигурации со строкой подключения, которая указывает на вашу учетную запись хранения. Файл app.config должен иметь примерно такой вид:

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

## Создание URI подписи общего доступа для контейнера

Для начала мы добавим метод для создания подписи общего доступа для нового контейнера. В этом случае подпись не связана с хранимой политикой доступа, поэтому содержит сведения URI с указанием времени окончания срока действия подписи и предоставляемых ей разрешений.

Во-первых, добавьте код для метода **Main()**, чтобы проверить подлинности доступа к вашей учетной записи хранения и создать новый контейнер:

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Insert calls to the methods created below here...
        
        //Require user input before closing the console window.
        Console.ReadLine();
    }

Затем добавьте новый метод, который создает подпись общего доступа для контейнера и возвращает URI подписи:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
        
        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
        
        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки, чтобы вызвать **GetContainerSasUri()** и записать URI подписи в окне консоли:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Скомпилируйте и запустите выходные данные URI подписи общего доступа для нового контейнера. Этот URI будет похож на следующий URI:

<https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D>

После выполнения кода созданная для контейнера подпись общего доступа будет действительна в течение следующих четырех часов. Эта подпись предоставляет клиенту разрешение на перечисление BLOB-объектов в контейнере и на запись нового BLOB-объекта в контейнер.

## Создание URI подписи общего доступа для BLOB-объекта

Далее мы напишем аналогичный код, создающий новый BLOB-объект в контейнере и подпись общего доступа для него. Эта подпись общего доступа не связаны с хранимой политикой доступа, поэтому она включает в себя время начала действия, время окончания действия и разрешения в виде URI.

Добавьте новый метод, который создает новый BLOB-объект и записывает туда какой-либо текст, а затем создает подпись общего доступа и возвращает URI подписи:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
        
        //Upload text to the blob. If the blob does not yet exist, it will be created. 
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
        
        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
        
        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Добавьте следующие строки в нижнюю часть метода **Main()**, чтобы вызвать **GetBlobSasUri()** перед вызовом **Console.ReadLine()** и записать URI подписи общего доступа в окне консоли:

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

Скомпилируйте и запустите выходные данные URI подписи общего доступа для нового BLOB-объекта. Этот URI будет похож на следующий URI:

<https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D>

## Создание хранимой политики доступа для контейнера

Теперь давайте создадим хранимую политику доступа для контейнера, которая будет определять ограничения для всех связанных с ней подписей общего доступа.

В предыдущих примерах мы указали время начала действия (явно или косвенно), время истечения срока действия, а также разрешения в самом URI подписи общего доступа. В следующих примерах мы укажем эти сведения в хранимой политике доступа, а не в подписи общего доступа. Это позволяет нам изменять такие ограничения без повторного выпуска подписи общего доступа.

Обратите внимание, что можно использовать одно или несколько ограничений в подписи общего доступа, а остальные ограничения задать в хранимой политике доступа. Однако время начала действия, время окончания срока действия и разрешения можно указать только в одном из этих двух мест. Например, нельзя задать разрешения в подписи общего доступа, а также в хранимой политике доступа.

Добавьте новый метод, который создает новую хранимую политику доступа и возвращает имя политики.

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
    {
        //Create a new stored access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };
        
        //Get the container's existing permissions.
        BlobContainerPermissions permissions = new BlobContainerPermissions();
        
        //Add the new policy to the container's permissions.
        permissions.SharedAccessPolicies.Clear();
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки, чтобы вызвать метод **CreateSharedAccessPolicy()**:

    //Create an access policy on the container, which may be optionally used to provide constraints for 
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

## Создание URI подписи общего доступа для контейнера, использующей политику доступа

Далее мы создадим другую подпись общего доступа для созданного ранее контейнера, но на этот раз мы свяжем подпись с политикой доступа, созданной в предыдущем примере.

Добавьте новый метод для создания другой подписи общего доступа для контейнера:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the 
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
        
        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки, чтобы вызвать метод **GetContainerSasUriWithPolicy**:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

## Создание URI подписи общего доступа для BLOB-объекта, использующей политику доступа

Наконец мы добавим аналогичный метод для создания другого BLOB-объекта и подписи общего доступа, сопоставленной с политикой доступа.

Добавьте новый метод для создания BLOB-объекта и подписи общего доступа:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
        
        //Upload text to the blob. If the blob does not yet exist, it will be created. 
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
        
        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки, чтобы вызвать метод **GetBlobSasUriWithPolicy**:

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Теперь метод **Main()** должен выглядеть следующим образом. Запустите его, чтобы записать URI подписей общего доступа в окне консоли, а затем скопируйте и вставьте их в текстовый файл для использования во второй части учебника.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();
        
        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();
        
        //Create an access policy on the container, which may be optionally used to provide constraints for 
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
        
        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        Console.ReadLine();
    }

При запуске консольного приложения GenerateSharedAccessSignatures его результат в окне консоли должен быть похож на следующее. Это подписи общего доступа, которые используются во второй части учебника.

![sas-console-output-1][sas-console-output-1]

# Часть 2: Создание консольного приложения для тестирования подписей общего доступа

Чтобы проверить подписи общего доступа, созданные в предыдущих примерах, мы создадим второе консольное приложение, использующее подписи для выполнения операций с контейнером и BLOB-объектом.

Заметьте, что если с момента прохождения первой части учебника прошло больше четырех часов, подписи, у которых время окончания срока действия было равно четырем часам, будут недействительными. Аналогичным образом, срок действия подписей, сопоставленных с хранимой политикой доступа, истекает через 10 часов. Если один или оба этих срока истекли, следует запустить код в первом консольном приложении для создания новых подписей общего доступа, которые можно использовать во второй части учебника.

В Visual Studio создайте новое консольное приложение Windows и назовите его **ConsumeSharedAccessSignatures**. Как и раньше, добавьте ссылки на **Microsoft.WindowsAzure.Configuration.dll** и **Microsoft.WindowsAzure.Storage.dll**.

Добавьте в начало файла Program.cs следующие инструкции **using**:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

В тело метода **Main()** добавьте следующие константы и обновите их значения на подписи общего доступа, созданные в первой части учебника.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

## Добавление метода для оценки операций с контейнером с использованием подписи общего доступа

Далее мы добавим метод, который проверяет некоторые характерные операции с контейнером, используя подпись общего доступа для этого контейнера. Обратите внимание, что подпись общего доступа используется для возврата ссылки на контейнер, обеспечивая проверку подлинности доступа к контейнеру на основании одной подписи.

Добавьте в класс Program.cs следующий метод:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<Uri> blobUris = new List<Uri>();

        try
        {
            //Write operation: write a new blob to the container. 
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //List operation: List the blobs in the container, including the one just added.
            foreach (ICloudBlob blobListing in container.ListBlobs())
            {
                blobUris.Add(blobListing.Uri);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Read operation: Get a reference to one of the blobs in the container and read it. 
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        try
        {
            //Delete operation: Delete a blob in the container.
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }

Измените метод **Main()** для вызова **UseContainerSAS()** с обеими подписями общего доступа, созданными для контейнера:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

## Добавление метода для оценки операций с BLOB-объектом с использованием подписи общего доступа

Наконец, мы добавим метод, который проверяет некоторые характерные операции с BLOB-объектом, используя подпись общего доступа для этого BLOB-объекта. В этом случае мы используем конструктор **CloudBlockBlob(String)**, передавая подпись общего доступа, чтобы возвратить ссылку на BLOB-объект. Никакая другая проверка подлинности не требуется, она основана только на подписи.

Добавьте в класс Program.cs следующий метод:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        try
        {
            //Write operation: write a new blob to the container. 
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Read operation: Read the contents of the blob.
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Delete operation: Delete the blob.
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }

Измените метод **Main()** для вызова **UseBlobSAS()** с обеими подписями общего доступа, созданными для BLOB-объекта:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Запустите консольное приложение и просмотрите выходные данные, чтобы узнать, какие операции разрешены для разных подписей. Выходные данные в окне консоли будут выглядеть следующим образом:

![sas-console-output-2][sas-console-output-2]

# Дальнейшие действия

[Подписи общего доступа, часть 1: Общие сведения о модели SAS][В части 1]

[Управление доступом к ресурсам хранилища Azure][Управление доступом к ресурсам хранилища Azure]

[Делегирование доступа с помощью подписи общего доступа (REST API)][Делегирование доступа с помощью подписи общего доступа (REST API)]

[Знакомство с подписью общего доступа для таблиц и очередей][Знакомство с подписью общего доступа для таблиц и очередей]

  [В части 1]: ../storage-dotnet-shared-access-signature-part-1/
  [пакет NuGet]: http://nuget.org/packages/WindowsAzure.Storage/ "пакет NuGet"
  [пакета Azure SDK для .NET]: http://www.windowsazure.com/ru-ru/downloads/
  [расширение диспетчера пакетов NuGet для Visual Studio]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
  [sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
  [sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
  [Управление доступом к ресурсам хранилища Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee393343.aspx
  [Знакомство с подписью общего доступа для таблиц и очередей]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx
