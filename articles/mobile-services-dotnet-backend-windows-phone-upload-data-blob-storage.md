<properties pageTitle="Использование мобильных служб для отправки изображений в хранилище больших двоичных объектов (Windows Phone) | Мобильные службы" metaKeywords="" description="Узнайте, как использовать мобильные службы для передачи образов в хранилище больших двоичных объектов Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/08/2014" ms.author="glenga" />

# Отправка образов в хранилище Azure с помощью мобильных служб

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

В этом разделе показано, как использовать мобильные службы Azure для передачи и сохранения созданных пользователем изображений в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов эффективнее хранить в службе хранения больших двоичных объектов Azure. 

Невозможно безопасно распространять вместе с клиентским приложением учетные данные, необходимые для защищенной передачи данных в службу хранения больших двоичных объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. Подпись общего доступа является типом учетных данных с быстрым окончанием срока действия&mdash;, в данном случае это 5 минут. Она безопасно возвращается мобильными службами в клиентское приложение. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы больших двоичных объектов, являются общедоступными.

В ходе выполнения этого учебника в [проект приложения GetStartedWithData](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) добавляются функциональные возможности для съемки фотографий и их передачи в Azure с помощью SAS, созданных мобильными службами. Этот учебник поможет выполнить следующие базовые действия для обновления простого приложения TodoList, чтобы передавать изображения в службу хранения больших двоичных объектов:

1. [Установите библиотеку клиента хранилища]
2. [Обновите клиентское приложение для захвата изображений]
3. [Установка клиента хранилища в проекте мобильных служб]
4. [Обновление определения TodoItem в модели данных]
5. [Обновите контроллер таблицы для создания учетных данных SAS]
6. [Передайте изображения для проверки приложения]

Для работы с данным учебником требуется следующее:

+ Microsoft Visual Studio 2013 или более поздняя версия.
+ [Пакет SDK Windows Phone 8.0] и выше
+ Диспетчер пакетов NuGet, установленный для Microsoft Visual Studio.
+ [Учетная запись хранилища Azure][Создание учетной записи хранилища]
+ Пройдите учебник [Добавление мобильных служб к существующему приложению](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)  

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Установка клиента хранилища для приложений из Магазина Windows

Чтобы иметь возможность использовать учетные данные SAS для отправки образов из вашего приложения в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища для приложений Магазина Windows.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой проект клиентского приложения, затем выберите **Управление пакетами NuGet**.

2. В левой области выберите категорию **Интернет**, выберите **Включить предварительный выпуск**, выполните поиск пакета **WindowsAzure.Storage-Preview**, щелкните **Установить** для пакета **Хранилище Azure**, затем примите условия лицензионных соглашений. 

  	![][2]

  	Это приведет к добавлению в проект библиотеки клиента для служб хранилища Azure.

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Установите библиотеку клиента хранилища]: #install-storage-client
[Обновите клиентское приложение для захвата изображений]: #add-select-images
[Установка клиента хранилища в проекте мобильных служб]: #storage-client-server
[Обновление определения TodoItem в модели данных]: #update-data-model
[Обновите контроллер таблицы для создания учетных данных SAS]: #update-scripts
[Передайте изображения для проверки приложения]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Отправить сообщение электронной почты с мобильных служб с помощью SendGrid]: /ru-ru/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[Планирование серверных заданий в мобильных службах]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started

[Портал управления Azure]: https://manage.windowsazure.com/
[Создание учетной записи хранения]: /ru-ru/documentation/articles/storage-create-storage-account/
[Библиотека клиентского хранилища Azure для приложений Магазина]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=35471


