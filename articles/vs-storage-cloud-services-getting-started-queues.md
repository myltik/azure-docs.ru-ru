<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro][vs-storage-aspnet-getting-started-intro]]

### Начало работы с хранилищем Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Большие двоичные объекты" class="current">Большие двоичные объекты</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Очереди">Очереди</a><a href="/en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Таблицы">Таблицы</a></div>

Хранилище очередей Azure — это служба хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколу HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения. Дополнительные сведения см. в статье [Как использовать хранилище очередей из .NET][Как использовать хранилище очередей из .NET].

Чтобы программным способом получить доступ к очередям в проекте облачной службы, необходимо сделать следующее.

1.  Получите сборку Microsoft.WindowsAzure.Storage.dll. Для этого можно использовать NuGet. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите "Управление пакетами NuGet". Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку "Установить", чтобы установить пакет хранилища Azure и зависимые компоненты. Добавьте ссылку на эту сборку в проект.
2.  Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

    с помощью Microsoft.WindowsAzure.Storage;
    с помощью Microsoft.WindowsAzure.Storage.Auth;
    с помощью Microsoft.WindowsAzure.Storage.Queue;

###### Получение строки подключения к хранилищу

Прежде чем что-либо делать с очередью, необходимо получить строку подключения для учетной записи хранилища, в котором будут располагаться очереди. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов облачных служб можно использовать тип **CloudConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include][vs-storage-getting-started-queues-include]]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Большие двоичные объекты]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Большие двоичные объекты"
  [Очереди]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-queues "Очереди"
  [Таблицы]: /en-us/documentation/articles/vs-storage-cloud-services-getting-started-tables "Таблицы"
  [Как использовать хранилище очередей из .NET]: http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-queues/ "Как использовать хранилище очередей из .NET"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
