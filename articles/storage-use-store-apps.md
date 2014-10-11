<properties linkid="manage-services-storage-using-storage-with-windows-store-apps" urlDisplayName="Azure Storage in Windows Store apps" pageTitle="Use Azure storage in Windows Store Apps | Azure" metaKeywords="" description="Learn how to use Azure blobs, queues, and tables to store data for a Windows Store app." metaCanonical="" services="storage" documentationCenter="" title="How to use Azure Storage in Windows Store Apps" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Использование хранилища Azure в приложениях Магазина Windows

В этом руководстве показано, как начать разработку приложения Магазина Windows, использующего хранилище Azure.

## Загрузка необходимых средств

-   [Visual Studio 2012][] упрощает процесс построения, отладки, локализации, упаковки и развертывания приложений Магазина Windows.
-   [Клиентская библиотека хранилища Windows Azure для среды выполнения Windows][] содержит типы, используемые для взаимодействия с хранилищем Azure и упакованные в компонент среды выполнения Windows.
-   [Средства служб данных WCF для приложений Магазина Windows][] расширяют возможности добавления ссылки на службу за счет поддержки OData на стороне клиента для приложений Магазина Windows в Visual Studio 2012.

## Разработка приложений

### Подготовка

Создайте новый проект приложения Магазина Windows в Visual Studio 2012:

![store-apps-storage-vs-project][]

Затем добавьте ссылку на клиентскую библиотеку хранилища Azure, щелкнув правой кнопкой мыши элемент **Ссылки**, выбрав значение **Добавить ссылку** и перейдя к загруженной клиентской библиотеке хранилища для среды выполнения Windows:

![store-apps-storage-choose-library][]

### Использование библиотеки со службами BLOB-объектов и очередей

На этом этапе приложение готово к связи со службами BLOB-объектов и очередей. Добавьте следующие операторы **using**, чтобы можно было напрямую ссылаться на типы хранилища Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Далее, добавьте кнопку на страницу. Добавьте следующий код для ее события **Click** и измените метод обработчика событий с помощью [ключевого слова async][]:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Предполагается, что у вас есть две строковых переменных, *accountName* и *accountKey*которые представляют имя вашей учетной записи хранения и ключ учетной записи, связанный с этой учетной записью.

Создайте и запустите приложение. При нажатии кнопки сначала выполняется проверка того, существует ли контейнер с именем *container1* в вашей учетной записи, в случае отсутствия он создается.

### Использование библиотеки со службой таблиц

Типы, используемые для связи со службой таблиц, зависят от служб данных WCF для библиотеки приложений Магазина Windows. Добавьте ссылку на необходимые библиотеки WCF с помощью консоли диспетчера пакетов:

![store-apps-storage-package-manager][]

Используйте следующую команду для направление диспетчера пакетов к определенному месту в вашем компьютере:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services.0\bin\NuGet"

Эта команда автоматически добавляет все необходимые ссылки на проект. Если вы не хотите использовать консоль диспетчера пакетов, можно добавить папку NuGet службы данных WCF в списке источников пакетов на локальном компьютере и затем добавить ссылку через пользовательский интерфейс, как описано в разделе [Управление пакетами NuGet с помощью диалогового окна][].

После создания ссылки на NuGet-пакет службы данных WCF измените код события **Click** вашей кнопки:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Этот код проверяет, существует ли таблица с именем *table1* в вашей учетной записи, и создает ее, если она отсутствует.

Можно также добавить ссылку на Microsoft.WindowsAzure.Storage.Table.dll в тот же загруженный пакет. Эта библиотека содержит дополнительные функциональные возможности, такие как сериализация на основе отражения и общие запросы. Обратите внимание, что эта библиотека не поддерживает JavaScript.

  [Visual Studio 2012]: http://msdn.microsoft.com/en-us/library/windows/apps/br211384
  [Клиентская библиотека хранилища Windows Azure для среды выполнения Windows]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx
  [Средства служб данных WCF для приложений Магазина Windows]: http://www.microsoft.com/en-us/download/details.aspx?id=30714
  [store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
  [store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
  [ключевого слова async]: http://msdn.microsoft.com/en-US/library/vstudio/hh156513.aspx
  [store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
  [Управление пакетами NuGet с помощью диалогового окна]: http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog
