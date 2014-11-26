<properties title="Начало работы с хранилищем Azure" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Приступая к работе][Приступая к работе]
> -   [Что произошло?][Что произошло?]

## Начало работы со службой хранилища Azure (проекты ASP.NET vNext)

> [AZURE.SELECTOR]
>
> -   [BLOB-объекты][BLOB-объекты]
> -   [Очереди][Приступая к работе]
> -   [Таблицы][Таблицы]

Хранилище очередей Azure — это служба хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколу HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения. Дополнительные сведения см. в статье [Как использовать хранилище очередей из .NET][Как использовать хранилище очередей из .NET].

Чтобы программным способом получить доступ к очередям в проектах ASP.NET vNext, необходимо сделать следующее.

1.  Добавьте следующее объявление пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

        using Microsoft.Framework.ConfigurationModel;

2.  Используйте следующий код для получения параметров настройки.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Получение строки подключения к хранилищу

Прежде чем что-либо делать с очередью, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта очередь. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. При использовании проекта ASP.NET vNext можно вызвать метод GET объекта Configuration, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Дополнительные сведения см. в разделе [ASP.NET vNext][ASP.NET vNext].

  [Приступая к работе]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Что произошло?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [BLOB-объекты]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Таблицы]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Как использовать хранилище очередей из .NET]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/ "Как использовать хранилище очередей из .NET"
  [ASP.NET vNext]: http://www.asp.net/vnext
