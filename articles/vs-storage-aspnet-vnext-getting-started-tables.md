<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro](../includes/vs-storage-aspnet-vnext-getting-started-intro.md)]

# Начало работы с хранилищем Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="Большие двоичные объекты" class="current">Большие двоичные объекты</a><a href="/ru-ru/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="Очереди">Очереди</a><a href="/ru-ru/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="Таблицы">Таблицы</a></div>

В службе хранилища таблиц Azure хранятся большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных. Дополнительные сведения см. в статье [Как использовать хранилище таблиц из .NET][Как использовать хранилище таблиц из .NET].

Чтобы программным способом получить доступ к очередям в проектах ASP.NET vNext, необходимо сделать следующее.

1.  Получите сборку Microsoft.WindowsAzure.Storage.dll. Для этого можно использовать NuGet. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите "Управление пакетами NuGet". Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку "Установить", чтобы установить пакет хранилища Azure и зависимые компоненты. Добавьте ссылку на эту сборку в проект.
2.  Установите пакет NuGet Microsoft.Framework.ConfigurationModel.Json.
3.  Добавьте следующее объявление пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

        using Microsoft.Framework.ConfigurationModel;

4.  Используйте следующий код для получения параметров настройки.

        var config = new Configuration();
        config.AddJsonFile("config.json");

### Получение строки подключения к хранилищу

Прежде чем что-либо делать с таблицей, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта таблица. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. При использовании проекта ASP.NET vNext можно вызвать метод GET объекта Configuration, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

  [vs-storage-aspnet-vnext-getting-started-intro]: ../includes/vs-storage-aspnet-vnext-getting-started-intro.md
  [Большие двоичные объекты]: /ru-ru/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs "Большие двоичные объекты"
  [Очереди]: /ru-ru/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues "Очереди"
  [Таблицы]: /ru-ru/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables "Таблицы"
  [Как использовать хранилище таблиц из .NET]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Как использовать хранилище таблиц из .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
