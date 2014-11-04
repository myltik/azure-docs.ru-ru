<properties title="Начало работы с хранилищем Azure" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Приступая к работе][Приступая к работе]
> -   [Что произошло?][Что произошло?]

## Начало работы со службой хранилища Azure (проекты ASP.NET vNext)

> [AZURE.SELECTOR]
>
> -   [BLOB-объекты][BLOB-объекты]
> -   [Очереди][Очереди]
> -   [Таблицы][Приступая к работе]

В службе хранилища таблиц Azure хранятся большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных. Дополнительные сведения см. в статье [Как использовать хранилище таблиц из .NET][Как использовать хранилище таблиц из .NET].

Чтобы программным способом получить доступ к очередям в проектах ASP.NET vNext, необходимо сделать следующее.

1.  Добавьте следующее объявление пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

        using Microsoft.Framework.ConfigurationModel;

2.  Используйте следующий код для получения параметров настройки.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Получение строки подключения к хранилищу

Прежде чем что-либо делать с таблицей, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта таблица. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. При использовании проекта ASP.NET vNext можно вызвать метод GET объекта Configuration, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

Дополнительные сведения см. в разделе [ASP.NET vNext][ASP.NET vNext].

  [Приступая к работе]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Что произошло?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [BLOB-объекты]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Очереди]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Как использовать хранилище таблиц из .NET]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Как использовать хранилище таблиц из .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
