<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-cloud-services-getting-started-intro][vs-storage-cloud-services-getting-started-intro]]

# Начало работы с хранилищем Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Большие двоичные объекты" class="current">Большие двоичные объекты</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Очереди">Очереди</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Таблицы">Таблицы</a></div>

Хранилище больших двоичных объектов Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов.

Для начала работы необходимо сначала создать учетную запись хранилища Azure, а затем один или несколько контейнеров в хранилище. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Дополнительные сведения о программном управлении большими двоичными объектами см. в статье [Как использовать хранилище больших двоичных объектов из .NET][Как использовать хранилище больших двоичных объектов из .NET].

Чтобы программным способом получить доступ к большим двоичным объектам в проекте облачной службы, необходимо сделать следующее.

1.  Получите сборку Microsoft.WindowsAzure.Storage.dll. Для этого можно использовать NuGet. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите "Управление пакетами NuGet". Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку "Установить", чтобы установить пакет хранилища Azure и зависимые компоненты. Добавьте ссылку на эту сборку в проект.
2.  Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

    с помощью Microsoft.WindowsAzure.Storage;
    с помощью Microsoft.WindowsAzure.Storage.Auth;
    с помощью Microsoft.WindowsAzure.Storage.Queue;

### Получение строки подключения к хранилищу

Прежде чем что-либо делать с большим двоичным объектом, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться этот объект. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов облачных служб можно использовать тип **CloudConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include][vs-storage-getting-started-blobs-include]]

  [vs-storage-cloud-services-getting-started-intro]: ../includes/vs-storage-cloud-services-getting-started-intro.md
  [Большие двоичные объекты]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Большие двоичные объекты"
  [Очереди]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues "Очереди"
  [Таблицы]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables "Таблицы"
  [Как использовать хранилище больших двоичных объектов из .NET]: http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/ "Как использовать хранилище больших двоичных объектов из .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
