<properties title="Getting Started with Azure Storage" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Что произошло?](/documentation/articles/vs-storage-aspnet-what-happened/)

## Начало работы со службой хранилища Azure (проекты ASP.NET)

> [AZURE.SELECTOR]
> - [BLOB-объекты](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Очереди](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Таблицы](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Хранилище очередей Azure - это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения. Подробнее см. в статье [Как использовать хранилище очередей из .NET](http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/).

Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####Получение строки подключения к хранилищу
Прежде чем выполнять какие-либо действия с очередью, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта очередь. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов ASP.NET можно использовать тип **ConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере кода.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Подробнее см. в статье [ASP.NET](http://www.asp.net).
