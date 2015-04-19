<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Начало работы с использованием хранилища очередей Azure в проекте облачной службы в Visual Studio" 
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
> - [Приступая к работе](vs-storage-cloud-services-getting-started-queues.md)
> - [Что произошло?](vs-storage-cloud-services-what-happened.md)

## Начало работы со службой хранилища Azure (проекты облачных служб)

> [AZURE.SELECTOR]
> - [BLOB-объекты](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Таблицы](vs-storage-cloud-services-getting-started-tables.md)

Хранилище очередей Azure - это служба для хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколам HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений до общего ограничения емкости учетной записи хранения. Подробнее см. в статье [Как использовать хранилище очередей из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET").

Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####Получение строки подключения к хранилищу
Прежде чем выполнять какие-либо действия с очередью, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта очередь. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов облачных служб можно использовать тип **CloudConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере кода.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]


<!--HONumber=46--> 
