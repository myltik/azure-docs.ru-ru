<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Начало работы с использованием хранилища таблиц Azure в проекте ASP.NET в Visual Studio" 
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
> - [Приступая к работе](vs-storage-aspnet-getting-started-tables.md)
> - [Что произошло?](vs-storage-aspnet-what-happened.md)

## Начало работы со службой хранилища Azure (проекты ASP.NET)

> [AZURE.SELECTOR]
> - [BLOB-объекты](vs-storage-aspnet-getting-started-blobs.md)
> - [Очереди](vs-storage-aspnet-getting-started-queues.md)
> - [Таблицы](vs-storage-aspnet-getting-started-tables.md)

В службе хранилища таблиц Azure можно хранить большие объемы структурированных данных. Эта служба - хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных.  Подробнее см. в статье [Как использовать хранилище больших двоичных объектов из .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

#####Получение строки подключения к хранилищу
Прежде чем выполнять какие-либо действия с таблицей, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта таблица. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов ASP.NET можно использовать тип **ConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере кода.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

Подробнее см. в статье [ASP.NET](http://www.asp.net).

<!--HONumber=46--> 
