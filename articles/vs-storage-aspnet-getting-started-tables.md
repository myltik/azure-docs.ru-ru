<properties title="Начало работы с хранилищем Azure" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Приступая к работе][Приступая к работе]
> -   [Что произошло?][Что произошло?]

## Начало работы со службой хранилища Azure (проекты ASP.NET)

> [AZURE.SELECTOR]
>
> -   [BLOB-объекты][BLOB-объекты]
> -   [Очереди][Очереди]
> -   [Таблицы][Приступая к работе]

В службе хранилища таблиц Azure хранятся большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных. Дополнительные сведения см. в статье [Как использовать хранилище таблиц из .NET][Как использовать хранилище таблиц из .NET].

Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

##### Получение строки подключения к хранилищу

Прежде чем что-либо делать с таблицей, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта таблица. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов ASP.NET можно использовать тип **ConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.AppSettings["<storageAccountName>_AzureStorageConnectionString"]);

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

Дополнительные сведения см. в разделе [ASP.NET][ASP.NET].

  [Приступая к работе]: /documentation/articles/vs-storage-aspnet-getting-started-tables/
  [Что произошло?]: /documentation/articles/vs-storage-aspnet-what-happened/
  [BLOB-объекты]: /documentation/articles/vs-storage-aspnet-getting-started-blobs/
  [Очереди]: /documentation/articles/vs-storage-aspnet-getting-started-queues/
  [Как использовать хранилище таблиц из .NET]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Как использовать хранилище таблиц из .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
  [ASP.NET]: http://www.asp.net
