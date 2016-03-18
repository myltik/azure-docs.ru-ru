<properties
	pageTitle="Использование службы хранилища Azure в приложениях Магазина Windows | Microsoft Azure"
	description="Узнайте, как создать приложение магазина Windows, которое использует BLOB-объекты, очереди, таблицы или хранилище файлов Azure."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm" />
<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="tamram"/>
	
# Использование службы хранилища Azure в приложениях Магазина Windows

## Обзор

В этом руководстве показано, как начать разработку приложения Магазина Windows, использующего хранилище Azure.

## Скачивание необходимых средств

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) упрощает процесс построения, отладки, локализации, упаковки и развертывания приложений Магазина Windows.
- [Клиентская библиотека хранилища Microsoft Azure для среды выполнения Windows](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) предоставляет библиотеку классов для работы со службой хранилища Azure.
- [Инструменты служб данных WCF для приложений Магазина Windows](http://www.microsoft.com/download/details.aspx?id=30714) расширяют возможности добавления ссылок на службу за счет поддержки OData на стороне клиента для приложений Магазина Windows в Visual Studio 2012 и более поздних версий.

## Разработка приложений

### Подготовка

Создайте новый проект приложения Магазина Windows в Visual Studio 2012 или более поздней версии:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Затем добавьте ссылку на клиентскую библиотеку службы хранилища Azure. Для этого щелкните правой кнопкой мыши элемент **Ссылки**, выберите пункт **Добавить ссылку** и перейдите к скачанной клиентской библиотеке хранилища для среды выполнения Windows:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### Использование библиотеки со службами BLOB-объектов и очередей

На этом этапе приложение готово к вызову служб BLOB-объектов и очередей Azure. Добавьте следующие операторы **using**, чтобы можно было напрямую ссылаться на типы хранилища Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Далее, добавьте кнопку на страницу. Добавьте следующий код к ее событию **Click** и измените метод обработчика событий с помощью [ключевого слова async](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

В этом коде есть две строковые переменные *accountName* и *accountKey*. Они представляют собой имя вашей учетной записи хранения и связанный ней ключ.

Создайте и запустите приложение. Нажав кнопку, вы сможете проверить свою учетную запись на наличие контейнера с именем *container1* и создать его в случае отсутствия.

### Использование библиотеки со службой таблиц

Типы, используемые для связи со службой таблиц Azure, зависят от служб данных WCF для библиотеки приложений Магазина Windows. Добавьте ссылку на необходимые библиотеки WCF с помощью консоли диспетчера пакетов:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Используйте следующую команду для направления диспетчера пакетов к определенному месту в вашем компьютере:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Эта команда автоматически добавляет все необходимые ссылки на проект. Кроме того, вы можете добавить папку NuGet служб данных WCF на локальном компьютере в список источников пакетов, а затем добавить ссылку через пользовательский интерфейс, как описано в разделе [Управление пакетами NuGet с помощью диалогового окна](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

После создания ссылки на NuGet-пакет службы данных WCF измените код события **Click** вашей кнопки:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Этот код позволяет проверить учетную запись на наличие таблицы с именем *table1* и создать ее в случае отсутствия.

Вы также можете добавить ссылку на библиотеку Microsoft.WindowsAzure.Storage.Table.dll, которая находится в том же скачанном пакете. Она содержит дополнительные возможности, такие как сериализация на основе отражения и общие запросы. Обратите внимание, что эта библиотека не поддерживает JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

<!---HONumber=AcomDC_0114_2016-->