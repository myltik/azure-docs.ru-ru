<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Начало работы с использованием хранилища BLOB-объектов Azure в проекте облачной службы в Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Что произошло?](/documentation/articles/vs-storage-cloud-services-what-happened/)

## Начало работы со службой хранилища Azure (проекты облачных служб)

> [AZURE.SELECTOR]
> - [BLOB-объекты](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Очереди](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Таблицы](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Хранилище больших двоичных объектов Azure - это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколам HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов.

Для начала работы необходимо сначала создать учетную запись хранилища Azure, а затем один или несколько контейнеров в хранилище. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Подробнее о программном обращении с большими двоичными объектами см в статье [Как использовать хранилище больших двоичных объектов из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET").

Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

#####Получение строки подключения к хранилищу
Прежде чем начать работу с большим двоичным объектом, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться этот объект. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов облачных служб можно использовать тип **CloudConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере кода.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

<!--HONumber=46--> 
