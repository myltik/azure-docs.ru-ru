<properties pageTitle="Использование мобильных служб для загрузки изображений в хранилище больших двоичных объектов (Магазин Windows) | Мобильные службы" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Отправка образов в хранилище Azure с помощью мобильных служб

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

В этом разделе показано, как использовать мобильные службы Azure для передачи и сохранения созданных пользователем изображений в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов эффективнее хранить в службе хранения больших двоичных объектов Azure. 

Невозможно безопасно распространять вместе с клиентским приложением учетные данные, необходимые для защищенной передачи данных в службу хранения больших двоичных объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. SAS, учетные данные с кратким сроком действия (в данном случае 5 минут), возвращаются безопасно в клиентское приложение мобильными службами. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы больших двоичных объектов, являются общедоступными.

В этом учебнике вы добавите некоторые функциональные возможности к учебному приложению мобильных служб для съемки фотографий и передачи изображений в Azure с помощью учетных данных SAS, созданных мобильными службами. Этот учебник поможет выполнить следующие базовые действия для обновления быстрого запуска мобильных служб, что позволит передавать изображения в службу хранения больших двоичных объектов:

1. [Установите библиотеку клиента хранилища]
2. [Обновите клиентское приложение для захвата изображений]
3. [Установка клиента хранилища в проекте мобильных служб]
4. [Обновление определения TodoItem в модели данных]
5. [Обновите контроллер таблицы для создания учетных данных SAS]
6. [Передайте изображения для проверки приложения]

Для работы с данным учебником требуется следующее:

+ Microsoft Visual Studio 2013 или более поздняя версия.
+ Диспетчер пакетов NuGet, установленный для Microsoft Visual Studio.
+ [Учетная запись хранилища Azure][Создание учетной записи хранилища]

Этот учебник создан на основе краткого руководства по мобильным службам. Прежде чем приступать к этому учебнику, необходимо пройти до конца учебник [Начало работы с мобильными службами]. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Установка клиента хранилища для приложений из Магазина Windows

Чтобы иметь возможность использовать учетные данные SAS для отправки образов из вашего приложения в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища для приложений Магазина Windows.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой проект клиентского приложения, затем выберите **Управление пакетами NuGet**.

2. В левой области выберите категорию **Интернет**, выберите **Включить предварительный выпуск**, выполните поиск пакета **WindowsAzure.Storage-Preview**, щелкните **Установить** для пакета **Хранилище Azure**, затем примите условия лицензионных соглашений. 

  	![][2]

  	Это приведет к добавлению в проект библиотеки клиента для служб хранилища Azure.

Далее необходимо будет обновить приложение quickstart для захвата и сохранения изображений.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[Установите библиотеку клиента хранилища]: #install-storage-client
[Обновите клиентское приложение для захвата изображений]: #add-select-images
[Установка клиента хранилища в проекте мобильных служб]: #storage-client-server
[Обновление определения TodoItem в модели данных]: #update-data-model
[Обновите контроллер таблицы для создания учетных данных SAS]: #update-scripts
[Передайте изображения для проверки приложения]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Отправить сообщение электронной почты с мобильных служб с помощью SendGrid]: /ru-ru/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Планирование серверных заданий в мобильных службах]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started

[Портал управления Azure]: https://manage.windowsazure.com/
[Создание учетной записи хранения]: /ru-ru/documentation/articles/storage-create-storage-account/
[Библиотека клиентского хранилища Azure для приложений Магазина]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=35471


