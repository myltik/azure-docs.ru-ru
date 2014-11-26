<properties linkId="mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" pageTitle="Use Mobile Services to upload images to blob storage (Windows Phone) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Отправка образов в хранилище Azure с помощью мобильных служб

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Windows Phone" class="current">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure, чтобы передавать и сохранять созданные изображения в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов (BLOB) более эффективно хранить в службе хранения BLOB-объектов Azure.

Невозможно безопасно передать в клиентском приложении учетные данные, необходимые для безопасной передачи данных в службу хранения BLOB-объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. SAS, учетные данные с кратким сроком действия (в данном случае 5 минут), возвращаются безопасно в клиентское приложение мобильными службами. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы BLOB-объектов, являются общедоступными.

В этом учебнике вы добавите некоторые функциональные возможности к быстрому запуску мобильных служб для съемки фотографий и передачи изображений в Azure с помощью учетных данных SAS, созданных мобильными службами. Этот учебник поможет выполнить следующие базовые действия для обновления быстрого запуска мобильных служб, что позволит передавать изображения в службу хранения BLOB-объектов:

1.  [Установите библиотеку клиента хранилища][Установите библиотеку клиента хранилища]
2.  [Обновите клиентское приложение для захвата изображений][Обновите клиентское приложение для захвата изображений]
3.  [Установка клиента хранилища в проекте мобильных служб][Установка клиента хранилища в проекте мобильных служб]
4.  [Обновление определения TodoItem в модели данных][Обновление определения TodoItem в модели данных]
5.  [Обновите контроллер таблицы для создания учетных данных SAS][Обновите контроллер таблицы для создания учетных данных SAS]
6.  [Передайте изображения для проверки приложения][Передайте изображения для проверки приложения]

Для работы с данным учебником требуется следующее:

-   Microsoft Visual Studio 2013 или более поздняя версия.
-   [Windows Phone SDK 8.0][Windows Phone SDK 8.0] и выше
-   Диспетчер пакетов NuGet, установленный для Microsoft Visual Studio.
-   [Учетная запись хранения Azure][Учетная запись хранения Azure]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

## <a name="install-storage-client"></a>Установка клиента хранения для приложений Магазина Windows

Чтобы иметь возможность использовать учетные данные SAS для отправки образов из вашего приложения в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища для приложений Магазина Windows.

1.  В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2.  В левой области выберите категорию **Интернет**, выберите **Включить предварительный выпуск**, выполните поиск пакета **WindowsAzure.Storage-Preview**, щелкните **Установить** для пакета **Хранилище Windows Azure**, затем примите условия лицензионных соглашений.

    ![][0]

    Это приведет к добавлению в проект библиотеки клиента для служб хранилища Azure.

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]



  [Установите библиотеку клиента хранилища]: #install-storage-client
  [Обновите клиентское приложение для захвата изображений]: #add-select-images
  [Установка клиента хранилища в проекте мобильных служб]: #storage-client-server
  [Обновление определения TodoItem в модели данных]: #update-data-model
  [Обновите контроллер таблицы для создания учетных данных SAS]: #update-scripts
  [Передайте изображения для проверки приложения]: #test
  [Windows Phone SDK 8.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=35471
  [Учетная запись хранения Azure]: /ru-ru/documentation/articles/storage-create-storage-account/
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
