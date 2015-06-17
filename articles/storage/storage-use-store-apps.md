<properties 
	pageTitle="Использование хранилища Azure в приложениях Магазина Windows" 
	description="Вы узнаете, как использовать BLOB-объекты Azure, очереди, таблицы для хранения данных для приложения Магазина Windows." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="tamram"/>





# Использование хранилища Azure в приложениях Магазина Windows

В этом руководстве показано, как начать разработку приложения Магазина Windows, использующего хранилище Azure.

## Загрузка необходимых средств ##

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) упрощает процесс построения, отладки, локализации, упаковки и развертывания приложений Магазина Windows.
- [Клиентская библиотека хранилища Microsoft Azure для среды выполнения Windows](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) предоставляет библиотеку классов для работы со службой хранилища Azure.
- [Инструменты служб данных WCF для приложений Магазина Windows](http://www.microsoft.com/ru-ru/download/details.aspx?id=30714) расширяют возможности добавления ссылки на службу за счет поддержки OData на стороне клиента для приложений Магазина Windows в Visual Studio версии 2012 или более поздних версий.

## Разработка приложений ##

<h3>Подготовка</h3>

Создайте новый проект приложения Магазина Windows в Visual Studio 2012 или более поздней версии:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Затем добавьте ссылку на клиентскую библиотеку хранилища Azure, щелкнув правой кнопкой мыши элемент **Ссылки**, выбрав значение **Добавить ссылку** и перейдя к загруженной клиентской библиотеке хранилища для среды выполнения Windows:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

<h3>Использование библиотеки со службами BLOB-объектов и очередей</h3>

На этом этапе приложение готово к вызову служб больших двоичных объектов и очередей. Добавьте следующие операторы **using**, чтобы можно было напрямую ссылаться на типы хранилища Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
Далее, добавьте кнопку на страницу. Добавьте следующий код для события **Click** и измените метод обработчика событий с помощью [async keyword](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
Предполагается, что у вас есть две строковые переменные,  *accountName* и  *accountKey*, которые представляют имя вашей учетной записи хранения и связанный с ней ключ учетной записи.

Создайте и запустите приложение. При нажатии кнопки сначала выполняется проверка того, существует ли контейнер с именем  *container1* в вашей учетной записи. В случае отсутствия он создается.

<h3>Использование библиотеки со службой таблиц</h3>

Типы, используемые для связи со службой таблиц, зависят от служб данных WCF для библиотеки приложений Магазина Windows. Добавьте ссылку на необходимые библиотеки WCF с помощью консоли диспетчера пакетов:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Используйте следующую команду для направления диспетчера пакетов к определенному месту в вашем компьютере:
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Эта команда автоматически добавляет все необходимые ссылки на проект. Если вы не хотите использовать консоль диспетчера пакетов, можно добавить папку NuGet службы данных WCF в списке источников пакетов на локальном компьютере и затем добавить ссылку через пользовательский интерфейс, как описано в разделе [Управление пакетами NuGet с помощью диалогового окна](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

После создания ссылки на пакет NuGet службы данных WCF измените код события **Click**:
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
Этот код проверяет, существует ли таблица с именем  *table1* в вашей учетной записи, и создает ее, если она отсутствует.

Можно также добавить ссылку на Microsoft.WindowsAzure.Storage.Table.dll в тот же загруженный пакет. Эта библиотека содержит дополнительные функциональные возможности, такие как сериализация на основе отражения и общие запросы. Обратите внимание, что эта библиотека не поддерживает JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

<!--HONumber=42-->
 