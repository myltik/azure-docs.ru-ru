<properties linkid="dev-java-how-to-on-premise-application-with-blob-storage" urlDisplayName="Image Gallery w/ Storage" pageTitle="On-premises application with blob storage (Java) | Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Learn how to create a console application that uploads an image to Azure, and then displays the image in your browser. Code samples in Java." metaCanonical="" services="storage" documentationCenter="Java" title="On-Premises Application with Blob Storage" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Локальное приложение с хранилищем больших двоичных объектов

В следующем примере показано, как использовать хранилище Azure для сохранения изображений в Azure. Ниже приведен код для консольного приложения, которое отправляет изображение в Azure, а затем создает HTML-файл, отображающий это изображение в браузере.

## Оглавление

-   [Предварительные требования][]
-   [Использование хранилища BLOB-объектов Azure для отправки файла][]
-   [Удаление контейнера][]

## <a name="bkmk_prerequisites"> </a>Предварительные требования

1.  Установленный пакет Java Developer Kit (JDK) версии 1.6 или более поздней.
2.  Установленный пакет Azure SDK.
3.  JAR-файл для библиотек Azure для Java и JAR-файлы для любых применимых зависимостей, установлены и указаны в пути построения, используемом компилятором Java. Сведения об установке библиотек Azure для Java см. в разделе [Загрузка Azure SDK для Java][].
4.  Настроенная учетная запись хранения Azure. Имя учетной записи и ключ учетной записи для учетной записи хранилища будут использоваться в следующем коде. См. раздел [Создание учетной записи хранения][] для получения информации о создании учетной записи и [Управление учетной записью][] для получения сведений об извлечении ключа учетной записи.
5.  Был создан локальный файл изображения, сохраненный в c:\\\\myimages\\\\image1.jpg. Можно также изменить конструктор **FileInputStream** в данном примере, чтобы использовать другой путь и другое имя для этого изображения.

[WACOM.INCLUDE [создание-учетная запись-примечание][]]

## <a name="bkmk_uploadfile"> </a>Использование хранилища BLOB-объектов Azure для отправки файла

Здесь представлены пошаговые процедуры; если вы хотите пропустить их, весь код представлен далее в этом разделе.

Начните работу с кодом, включив операции импорта для базовых классов хранилища Azure, классов клиента BLOB-объектов Azure, классов ввода-вывода Java и класса **URISyntaxException**:

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Объявите класс с именем **StorageSample** и включите открывающую скобку **{**.

    public class StorageSample {

В классе **StorageSample** объявите строковую переменную, которая будет содержать протокол конечной точки по умолчанию, имя вашей учетной записи хранения и ключ доступа к хранилищу, как указано в вашей учетной записи хранения Azure. Замените значения заполнителей **your\_account\_name** и **your\_account\_key** именем и ключом учетной записи,
соответственно.

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

Добавьте объявление **main**, включите блок **try** и включит необходимые открывающие скобки, **{**.

    public static void main(String[] args) 
    {
        try
        {

Объявите переменные следующего типа (описания их использования приведены в этом примере):

-   **CloudStorageAccount**: используется для инициализации объекта учетной записи с
    использованием имени и ключа учетной записи хранения Azure, а также для создания клиентского
    BLOB-объекта.
-   **CloudBlobClient**: используется для доступа к службе BLOB-объектов.
-   **CloudBlobContainer**: используется для создания контейнера BLOB-объектов, перечисления
    BLOB-объектов в контейнере и удаления контейнера.
-   **CloudBlockBlob**: используется для отправки локального файла изображения
    в контейнер.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Присвойте значение переменной **account**.

    account = CloudStorageAccount.parse(storageConnectionString);

Присвойте значение переменной **serviceClient**.

    serviceClient = account.createCloudBlobClient();

Присвойте значение переменной **container**. Мы получим ссылку на контейнер с именем **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Создайте контейнер. Этот метод создает контейнер, если он не существует (и возвращает значение **true**). Если контейнер существует, он возвращает значение **false**. Вместо **createIfNotExist** можно использовать метод **create**  (который возвращает ошибку, если контейнер уже существует).

    container.createIfNotExist();

Настройте анонимный доступ к контейнеру.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Получите ссылку на BLOB-блок, представляющий BLOB-объект в хранилище Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Используйте конструктор **файлов** для получения ссылки на локально созданный файл, который требуется отправить. (Перед выполнением этого кода убедитесь, что вы создали этот файл.)

    File fileReference = new File ("c:\myimages\image1.jpg");

Отправьте локальный файл посредством вызова метода **CloudBlockBlob.upload** . Первым параметром метода **CloudBlockBlob.upload** является объект **FileInputStream**, который представляет локальный файл, предназначенный для отправки  в хранилище Azure. Вторым параметром является размер
файла в байтах.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Вызовите вспомогательную функцию с именем **MakeHTMLPage**, чтобы создать базовую страницу HTML,
содержащую элемент **\<image\>** с источником в виде BLOB-объекта, присутствующего в вашей учетной записи хранения Azure. (Код **MakeHTMLPage** будет обсуждаться далее в этом разделе.)

    MakeHTMLPage(container);

Распечатайте сообщение о состоянии и сведения о созданной странице HTML.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Закройте блок **try**, вставив закрывающую скобку: **}**

Реализуйте обработку следующих исключений:

-   **FileNotFoundException**: может выдаваться конструкторами **FileInputStream**
    или **FileOutputStream**.
-   **StorageException**: может выдаваться клиентской
    библиотекой хранилища Azure.
-   **URISyntaxException**: может выдаваться методом **ListBlobItem.getUri**
    .
-   **Exception**: обработка универсального исключения.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Закройте **main**, вставив закрывающую скобку: **}**

Создайте метод с именем **MakeHTMLPage** для создания базовой страницы HTML. Этот метод имеет параметр типа **CloudBlobContainer**, который будет использоваться для выполнения итерации по списку отправленных BLOB-объектов. Этот метод создает исключения типа **FileNotFoundException**, которые создает конструктор **FileOutputStream** и **URISyntaxException**,  создаваемое методом **ListBlobItem.getUri**. Вставьте открывающую скобку, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Создайте локальный файл с именем **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Выполните запись в локальный файл, добавив элементы **\<html\>**, **\<header\>**, и **\<body\>**

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Выполните итерацию по списку отправленных BLOB-объектов. Для каждого blob-объекта страница HTML создает элемент **\<img\>**, который имеет собственный атрибут **src**, отправленный в URI blob-объекта, так как он существует в учетной записи хранения Azure. Хотя в этот пример было добавлено всего одно изображение, при добавлении нескольких изображений код будет выполнять итерации по всем этим изображениям.

Для простоты в этом примере предполагается, что каждый отправленный BLOB-объект является изображением. Если  вы обновили BLOB-объекты, отличные от изображений или являющиеся не BLOB-блоками,а BLOB-страницами, скорректируйте код соответствующим образом.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Закройте элементы **\<body\>** и **\<html\>**.

    stream.println("</body>");
    stream.println("</html>");

Закройте локальный файл.

    stream.close();

Закройте **MakeHTMLPage**, вставив закрывающую скобку: **}**

Закройте **StorageSample**, вставив закрывающую скобку: **}**

Ниже приведен полный код для этого примера. Не забудьте изменить значения заполнителей **your\_account\_name** и **your\_account\_key** на имя и ключ учетной записи,
соответственно.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor 
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_name"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            }
            catch (FileNotFoundException fileNotFoundException)
            {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (URISyntaxException uriSyntaxException)
            {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Кроме отправки локального файла изображения в хранилище Azure,в этом примере кода создается локальный файл с именем index.html, который можно открыть в браузере для просмотра отправленного изображения.

Поскольку код содержит имя учетной записи и ключ учетной записи, обеспечьте безопасность исходного кода.

## <a name="bkmk_deletecontainer"> </a>Удаление контейнера

Поскольку за хранилище начисляется плата, вам может потребоваться удалить контейнер **gettingstarted** после окончания экспериментов с этим примером. Чтобы удалить контейнер, используйте метод **CloudBlobContainer.delete**
:

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Чтобы вызвать метод **CloudBlobContainer.delete** выполняется тот же процесс инициализации объектов **CloudStorageAccount**, **ClodBlobClient**, **CloudBlobContainer**, как было показано для метода **createIfNotExist**. Ниже приведен полный пример, который удаляет контейнер с именем **gettingstarted**.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

    public class DeleteContainer {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_key"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();
                
                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Обзор других классов и методов хранилища BLOB-объектов см. в разделе [Использование службы хранилища BLOB-объектов из Java][].

  [Предварительные требования]: #bkmk_prerequisites
  [Использование хранилища BLOB-объектов Azure для отправки файла]: #bkmk_uploadfile
  [Удаление контейнера]: #bkmk_deletecontainer
  [Загрузка Azure SDK для Java]: http://www.windowsazure.com/en-us/develop/java/
  [Создание учетной записи хранения]: http://www.windowsazure.com/en-us/manage/services/storage/how-to-create-a-storage-account/
  [Управление учетной записью]: http://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/
  [создание-учетная запись-примечание]: ../includes/create-account-note.md
  [Использование службы хранилища BLOB-объектов из Java]: http://www.windowsazure.com/en-us/develop/java/how-to-guides/blob-storage/
