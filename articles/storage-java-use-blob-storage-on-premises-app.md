<properties urlDisplayName="Image Gallery w/ Storage" pageTitle="Локальное приложение с хранилищем больших двоичных объектов (Java) - Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Learn how to create a console application that uploads an image to Azure, and then displays the image in your browser. Code samples in Java." metaCanonical="" services="storage" documentationCenter="Java" title="On-Premises Application with Blob Storage" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Локальное приложение с хранилищем больших двоичных объектов

В следующем примере показано, как использовать хранилище Azure для
сохранения изображений в Azure. Ниже представлен код для консольного
приложения, которое загружает изображение в Azure и затем создает
HTML-файл для отображения изображения в вашем браузере.

## Оглавление

-   [Предварительные условия][]
-   [Использование хранилища больших двоичных объектов Azure для отправки файла][]
-   [Удаление контейнера][]

## <a name="bkmk_prerequisites"> </a>Предварительные требования

1.  Установленный пакет Java Developer Kit (JDK) версии 1.6 или более поздней.
2.  Установленный пакет Azure SDK.
3.  JAR-файл для библиотек Azure для Java и JAR-файлы для любых применимых зависимостей, установлены и указаны в пути сборки, который используется компилятором Java. Дополнительную информацию об установке библиотек Azure для Java см. в разделе "Загрузка пакета Azure SDK для Java"].
4.  Настроенная учетная запись хранения Azure. Имя учетной записи и ключ учетной записи для учетной записи хранилища будут использоваться в следующем коде. Ознакомьтесь с разделом [Создание учетной записи хранения], чтобы получить информацию о создании учетной записи хранения, а также изучите раздел [Управление учетными записями хранения] для получения информации о том, как получить ключ учетной записи.
5.  Вы создали локальный файл изображения, который был сохранен с именем c:\\myimages\\image1.jpg. Можно также изменить конструктор **FileInputStream** из данного примера, чтобы использовать другой путь и другое имя файла для изображения.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a name="bkmk_uploadfile"> </a>Использование хранилища BLOB-объектов Azure для отправки файла

В этом разделе пошагово описывается данная процедура. Если вы хотите пропустить описание шагов, полная версия кода будет представлена далее в этом разделе.

Начните писать код с импорта основных необходимых библиотек классов Azure,
, клиентских классов больших двоичных объектов Azure, классов Java для операций ввода-вывода и
класса **URISyntaxException**:

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Объявите класс с именем **StorageSample** и включите открывающую скобку 
**{**.

    public class StorageSample {

В классе **StorageSample** объявите строковую переменную, которая
будет содержать протокол конечной точки по-умолчанию, имя учетной записи хранения и 
ключ доступа к хранилищу, как это указано в вашей учетной записи хранения Azure
. Замените значения заполнителей **your\_account\_name** и
**your\_account\_key** на имя учетной записи и ключ учетной записи
соответственно.

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

Добавьте объявление метода **main**, включите в него конструкцию **try** и
необходимые скобки **{**.

    public static void main(String[] args) 
    {
        try
        {

Объявите переменные следующего типа (описание их использования можно найти в
данном примере):

-   **CloudStorageAccount**: используется для инициализации объекта учетной записи с использованием имени и ключа учетной записи хранения Azure, а также для создания большого двоичного объекта клиента.
-   **CloudBlobClient**: используется для доступа к службе BLOB-объектов.
-   **CloudBlobContainer**: используется для создания контейнера больших двоичных объектов, перечисления больших двоичных объектов в контейнере и удаления контейнера.
-   **CloudBlockBlob**: используется для отправки локального файла изображения в контейнер.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Присвойте значение переменной **account**.

    account = CloudStorageAccount.parse(storageConnectionString);

Присвойте значение переменной **serviceClient**.

    serviceClient = account.createCloudBlobClient();

Присвойте значение переменной **container**. Мы создадим ссылку на
контейнер с именем **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Создайте контейнер. Этот метод создает контейнер, если он не существует (и возвращает значение **true**). Если контейнер не существует, метод вернет значение **false**. Вместо метода **createIfNotExist** можно использовать метод **create** (который возвращает ошибку, если контейнер уже существует).

    container.createIfNotExist();

Настройте анонимный доступ к контейнеру.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Получите ссылку на блок большого двоичного объекта, который представляет собой большой двоичных объект в
хранилище Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Используйте конструктор **File**, чтобы получить ссылку на локально созданный файл, который требуется отправить. (Перед выполнением этого кода убедитесь,что вы создали этот файл.)

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Отправьте локальный файл посредством вызова метода **CloudBlockBlob.upload**. Первым параметром метода **CloudBlockBlob.upload** является объект **FileInputStream**, который представляет локальный файл, предназначенный для отправки в хранилище Azure. Вторым параметром является размер файла в байтах.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Вызовите вспомогательную функцию с именем **MakeHTMLPage**, чтобы создать базовую страницу HTML, содержащую элемент **&lt;image&gt;** с источником в виде большого двоичного объекта, присутствующего в вашей учетной записи хранения Azure. (Код метода **MakeHTMLPage** будет обсуждаться далее в этом разделе.)

    MakeHTMLPage(container);

Распечатайте сообщение о состоянии и сведения о созданной странице HTML.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Закройте блок **try**, вставив закрывающую скобку **}**

Реализуйте обработку следующих исключений:

-   **FileNotFoundException**: может вызываться конструкторами **FileInputStream**  или **FileOutputStream**.
-   **StorageException**: может вызываться клиентской библиотекой хранилища Azure.
-   **URISyntaxException**: может вызываться методом **ListBlobItem.getUri** .
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

Закройте метод **main**, вставив закрывающую скобку **}**

Создайте метод с именем **MakeHTMLPage** для создания базовой страницы HTML. Этот метод содержит параметр типа **CloudBlobContainer**, который будет использоваться для выполнения итерации по списку отправленных больших двоичных объектов. Данный метод может вызвать исключение типа **FileNotFoundException** (которое может быть вызвано конструктором) или исключения типов **FileOutputStream** и **URISyntaxException** (которые могут быть вызваны методом **ListBlobItem.getUri**). Укажите открывающую скобку **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Создайте локальный файл с именем **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Выполните запись в локальный файл, добавив элементы **&lt;html&gt;**, **&lt;header&gt;** и **&lt;body&gt;**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Выполните итерацию по списку отправленных BLOB-объектов. Для каждого большого двоичного объекта на странице HTML создайте элемент **&lt;img&gt;**, атрибут **src** которого отправляется в универсальный код ресурса (URI) большого двоичного объекта в том виде, в котором он существует в вашей учетной записи хранения Azure. Хотя в этот пример было добавлено всего одно изображение, при добавлении нескольких изображений код будет выполнять итерации по всем изображениям.

Для простоты в этом примере предполагается, что каждый отправленный BLOB-объект является изображением. Если вы обновили большие двоичные объекты, отличные от изображений или которые являются страницами больших двоичных объектов, а не блоками больших двоичных объектов, скорректируйте код соответствующим образом.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Закройте элементы **&lt;body&gt;** и **&lt;html&gt;**.

    stream.println("</body>");
    stream.println("</html>");

Закройте локальный файл.

    stream.close();

Закройте метод **MakeHTMLPage**, вставив закрывающую скобку **}**

Закройте метод **StorageSample**, вставив закрывающую скобку **}**

Ниже приведен полный код для этого примера. Не забудьте заменить значения заполнителей **your\_account\_name** и **your\_account\_key** на имя вашей учетной записи и ключ учетной записи соответственно.

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

Кроме отправки локального файла изображения в хранилище Azure, в этом примере кода создается локальный файл с именем index.html, который можно открыть в браузере для просмотра отправленного изображения.

Поскольку код содержит имя учетной записи и ключ учетной записи, обеспечьте безопасность исходного кода.

## <a name="bkmk_deletecontainer"> </a>Удаление контейнера

Поскольку за использование хранилища начисляется плата, вам может потребоваться удалить контейнер **gettingstarted** после окончания работы с данным примером. Чтобы удалить контейнер, используйте код **CloudBlobContainer.delete**:

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Чтобы вызвать метод **CloudBlobContainer.delete**, необходимо инициализировать объекты **CloudStorageAccount**, **ClodBlobClient** и **CloudBlobContainer** (процесс инициализации абсолютно такой же, как и для метода **createIfNotExist**). Ниже приведен полный пример удаления контейнера с именем **gettingstarted**.

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

Обзор других классов и методов хранилища больших двоичных объектов см. в разделе [Использование службы хранилища больших двоичных объектов из Java].

  [Предварительные условия]: #bkmk_prerequisites
  [Использование хранилища больших двоичных объектов Azure для отправки файла]: #bkmk_uploadfile
  [Удаление контейнера]: #bkmk_deletecontainer
  [Скачивание пакета SDK для Azure для Java]: http://www.windowsazure.com/ru-ru/develop/java/
  [Создание учетной записи хранения]: http://www.windowsazure.com/ru-ru/manage/services/storage/how-to-create-a-storage-account/
  [Как управлять учетными записями хранения]: http://www.windowsazure.com/ru-ru/manage/services/storage/how-to-manage-a-storage-account/
  [Как использовать службу хранилища BLOB-объектов из Java]: http://www.windowsazure.com/ru-ru/develop/java/how-to-guides/blob-storage/

<!--HONumber=35.1-->
