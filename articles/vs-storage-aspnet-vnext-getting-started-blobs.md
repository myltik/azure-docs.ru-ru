<properties title="Начало работы с хранилищем Azure" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Приступая к работе][Приступая к работе]
> -   [Что произошло?][Что произошло?]

## Начало работы со службой хранилища Azure (проекты ASP.NET vNext)

> [AZURE.SELECTOR]
>
> -   [BLOB-объекты][Приступая к работе]
> -   [Очереди][Очереди]
> -   [Таблицы][Таблицы]

Хранилище больших двоичных объектов Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов.

Для начала работы необходимо сначала создать учетную запись хранилища Azure, а затем один или несколько контейнеров в хранилище. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Дополнительные сведения о программном управлении большими двоичными объектами см. в статье [Как использовать хранилище больших двоичных объектов из .NET][Как использовать хранилище больших двоичных объектов из .NET].

Чтобы программным способом получить доступ к большим двоичным объектам в проектах ASP.NET vNext, необходимо сделать следующее.

1.  Добавьте следующее объявление пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

        using Microsoft.Framework.ConfigurationModel;

2.  Используйте следующий код для получения параметров настройки.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Получение строки подключения к хранилищу

Прежде чем что-либо делать с большим двоичным объектом, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться этот объект. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. При использовании проекта ASP.NET vNext можно вызвать метод GET объекта Configuration, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

Дополнительные сведения см. в разделе [ASP.NET vNext][ASP.NET vNext].

  [Приступая к работе]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Что произошло?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Очереди]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Таблицы]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Как использовать хранилище больших двоичных объектов из .NET]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-blobs/ "Как использовать хранилище больших двоичных объектов из .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
