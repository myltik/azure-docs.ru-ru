<properties linkid="manage-services-storage-using-storage-with-windows-store-apps" urlDisplayName="Хранилище Azure в приложениях Магазина Windows" pageTitle="Использование хранилища Azure в приложениях Магазина Windows | Azure" metaKeywords="" description="Узнайте, как использовать BLOB-объекты, очереди и таблицы для хранения данных для приложения хранилища Azure " metaCanonical="" services="storage" documentationCenter="" title="Использование хранилища Azure в приложениях Магазина Windows" authors="" solutions="" manager="" editor="" />





# Использование хранилища Azure в приложениях Магазина Windows

В этом руководстве показано, как начать разработку приложения Магазина Windows, использующего хранилище Azure.

## Загрузка необходимых средств ##

- [Visual Studio 2012](http://msdn.microsoft.com/ru-ru/library/windows/apps/br211384) упрощает процесс построения, отладки, локализации, упаковки и развертывания приложений Магазина Windows.
- [Клиентская библиотека хранилища Azure для среды выполнения Windows](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) содержит типы, используемые для взаимодействия с хранилищем Azure и упакованные в компонент среды выполнения Windows.
- [Средства служб данных WCF для приложений Магазина Windows](http://www.microsoft.com/ru-ru/download/details.aspx?id=30714) расширяют возможности добавления ссылки на службу за счет поддержки OData на стороне клиента для приложений Магазина Windows в Visual Studio 2012.

## Разработка приложений ##

<h3>Подготовка</h3>

Создайте новый проект приложения Магазина Windows в Visual Studio 2012:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Затем добавьте ссылку на клиентскую библиотеку хранилища Azure, щелкнув правой кнопкой мыши элемент **Ссылки**, выбрав значение **Добавить ссылку** и перейдя к загруженной клиентской библиотеке хранилища для среды выполнения Windows:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

<h3>Использование библиотеки со службами BLOB-объектов и очередей</h3>

На этом этапе приложение готово к связи со службами BLOB-объектов и очередей. Добавьте следующие операторы **using**, чтобы можно было напрямую ссылаться на типы хранилища Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
Добавьте кнопку на страницу. Добавьте следующий код для ее события **Click** и измените метод обработчика событий с помощью [ключевого слова async](http://msdn.microsoft.com/ru-ru/library/vstudio/hh156513.aspx).
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
Предполагается, что у вас есть две строковых переменных *accountName* и *accountKey*, которые представляют имя вашей учетной записи хранения и ключ учетной записи, связанный с этой учетной записью.

Создайте и запустите приложение. При нажатии кнопки сначала выполняется проверка того, существует ли контейнер с именем *container1* в вашей учетной записи, в случае отсутствия он создается.

<h3>Использование библиотеки со службой таблиц</h3>

Типы, используемые для связи со службой таблиц, зависят от служб данных WCF для библиотеки приложений Магазина Windows. Добавьте ссылку на необходимые библиотеки WCF с помощью консоли диспетчера пакетов:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Используйте следующую команду, чтобы указать диспетчеру пакетов расположение на своем компьютере:
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Эта команда автоматически добавляет все необходимые ссылки на проект. Если вы не хотите использовать консоль диспетчера пакетов, можно добавить папку NuGet службы данных WCF в списке источников пакетов на локальном компьютере и затем добавить ссылку через пользовательский интерфейс, как описано в разделе [Управление пакетами NuGet с помощью диалогового окна](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

После создания ссылки на NuGet-пакет службы данных WCF измените код события **Click** вашей кнопки:
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
Этот код проверяет, существует ли таблица с именем *table1* в вашей учетной записи, и создает ее, если она отсутствует.

Можно также добавить ссылку на Microsoft.WindowsAzure.Storage.Table.dll в тот же загруженный пакет. Эта библиотека содержит дополнительные функциональные возможности, такие как сериализация на основе отражения и общие запросы. Обратите внимание, что эта библиотека не поддерживает JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

