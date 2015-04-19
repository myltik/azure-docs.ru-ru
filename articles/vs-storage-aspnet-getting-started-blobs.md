<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](vs-storage-aspnet-getting-started-blobs.md)
> - [Что случилось](vs-storage-aspnet-what-happened.md)

## Начало работы со службой хранилища Azure (проекты ASP.NET)

> [AZURE.SELECTOR]
> - [BLOB-объекты](vs-storage-aspnet-getting-started-blobs.md)
> - [Очереди](vs-storage-aspnet-getting-started-queues.md)
> - [Таблицы](vs-storage-aspnet-getting-started-tables.md)

Хранилище больших двоичных объектов Azure - это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов.

Для начала работы необходимо сначала создать учетную запись хранилища Azure, а затем один или несколько контейнеров в хранилище. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Подробнее о программном обращении с большими двоичными объектами см. в статье [Как использовать хранилище больших двоичных объектов из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET").

Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

##### Получение строки подключения к хранилищу
Прежде чем что-либо делать с большим двоичным объектом, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться этот объект. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи. Для проектов ASP.NET можно использовать тип **ConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

Дополнительные сведения см. в разделе [ASP.NET](http://www.asp.net).
<!--HONumber=42-->
