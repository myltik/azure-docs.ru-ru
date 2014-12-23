<properties title="Getting Started with Azure Storage" pageTitle="Начало работы с хранилищем Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)
> - [Что произошло?](/documentation/articles/vs-storage-cloud-services-what-happened/)

##Начало работы со службой хранилища Azure (проекты облачных служб)

> [AZURE.SELECTOR]
> - [BLOB-объекты](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Очереди](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Таблицы](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

В службе хранилища таблиц Azure можно хранить большие объемы структурированных данных. Эта служба - хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных. Подробнее см. в статье [Как использовать хранилище больших двоичных объектов из .NET](http://azure.microsoft.com/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

#####Получение строки подключения к хранилищу
Прежде чем выполнять какие-либо действия с таблицей, необходимо получить строку подключения для учетной записи хранилища, в котором будет располагаться эта таблица. Можно использовать тип **CloudStorageAccount** для представления сведений учетной записи хранилища. Для проектов облачных служб можно использовать тип **CloudConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере кода.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]
