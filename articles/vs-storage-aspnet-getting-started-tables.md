<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro][vs-storage-aspnet-getting-started-intro]]

### Начало работы с хранилищем Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/vs-storage-aspnet-getting-started-blobs" title="Большие двоичные объекты" class="current">Большие двоичные объекты</a><a href="/ru-ru/documentation/articles/vs-storage-aspnet-getting-started-queues" title="Очереди">Очереди</a><a href="/ru-ru/documentation/articles/vs-storage-aspnet-getting-started-tables" title="Таблицы">Таблицы</a></div>

В службе хранилища таблиц Azure хранятся большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных. Дополнительные сведения см. в статье [Как использовать хранилище таблиц из .NET][Как использовать хранилище таблиц из .NET].

Чтобы программным способом получить доступ к таблицам в проектах ASP.NET, необходимо сделать следующее.

1.  Получите сборку Microsoft.WindowsAzure.Storage.dll. Для этого можно использовать NuGet. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите "Управление пакетами NuGet". Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку "Установить", чтобы установить пакет хранилища Azure и зависимые компоненты. Добавьте ссылку на эту сборку в проект.
2.  Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

    с помощью Microsoft.WindowsAzure.Storage;
    с помощью Microsoft.WindowsAzure.Storage.Auth;
    с помощью Microsoft.WindowsAzure.Storage.Queue;

###### Получение строки подключения к хранилищу

Прежде чем что-либо делать с таблицей, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта таблица. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов ASP.NET можно использовать тип **ConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include][vs-storage-getting-started-tables-include]]

  [vs-storage-aspnet-getting-started-intro]: ../includes/vs-storage-aspnet-getting-started-intro.md
  [Большие двоичные объекты]: /ru-ru/documentation/articles/vs-storage-aspnet-getting-started-blobs "Большие двоичные объекты"
  [Очереди]: /ru-ru/documentation/articles/vs-storage-aspnet-getting-started-queues "Очереди"
  [Таблицы]: /ru-ru/documentation/articles/vs-storage-aspnet-getting-started-tables "Таблицы"
  [Как использовать хранилище таблиц из .NET]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Как использовать хранилище таблиц из .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
