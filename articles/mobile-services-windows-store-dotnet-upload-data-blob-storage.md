<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage and access the images from your Windows Store app." metaCanonical="" services="mobile-services,storage" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Отправка образов в хранилище Azure с помощью мобильных служб

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="Серверная часть .NET" >Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure, чтобы передавать и сохранять созданные изображения в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов (BLOB) более эффективно хранить в службе хранения BLOB-объектов Azure.

Невозможно безопасно передать в клиентском приложении учетные данные, необходимые для безопасной передачи данных в службу хранения BLOB-объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. SAS, учетные данные с кратким сроком действия (в данном случае 5 минут), возвращаются безопасно в клиентское приложение мобильными службами. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы BLOB-объектов, являются общедоступными.

В этом учебнике вы добавите некоторые функциональные возможности к быстрому запуску мобильных служб для съемки фотографий и передачи изображений в Azure с помощью учетных данных SAS, созданных мобильными службами. Этот учебник поможет выполнить следующие базовые действия для обновления быстрого запуска мобильных служб, что позволит передавать изображения в службу хранения BLOB-объектов:

1.  [Установите библиотеку клиента хранилища][Установите библиотеку клиента хранилища]
2.  [Обновите сценарий вставки для создания учетных данных SAS][Обновите сценарий вставки для создания учетных данных SAS]
3.  [Обновите клиентское приложение для захвата изображений][Обновите клиентское приложение для захвата изображений]
4.  [Передайте изображения для проверки приложения][Передайте изображения для проверки приложения]

Для работы с данным учебником требуется следующее:

-   Microsoft Visual Studio 2012 Express для Windows 8 или более поздней версии.
-   [Учетная запись хранения Azure][Учетная запись хранения Azure]
-   Камера или другое устройство для съемки, подключенное к компьютеру.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <a name="install-storage-client"></a>Установка клиента хранения для приложений Магазина Windows

Чтобы иметь возможность использовать учетные данные SAS для передачи изображений в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища для приложений Магазина Windows.

1.  В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2.  На левой панели выберите категорию **В сети**, выполните поиск `WindowsAzure.Storage`, щелкните пункт **Установить** для пакета **службы хранилище Azure**, а затем примите условия лицензионного соглашения.

    ![][0]

    Это приведет к добавлению в проект библиотеки клиента для служб хранилища Azure.

Далее необходимо будет обновить приложение quickstart для захвата и сохранения изображений.

## <a name="update-scripts"></a>Обновление зарегистрированных скриптов вставки на портале управления

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы смогли безопасно передать изображения, интегрировав мобильную службу и службу BLOB-объектов, ознакомьтесь с некоторыми темами, посвященными серверным службам и интеграции:

-   [Отправить сообщение электронной почты с мобильных служб с помощью SendGrid][Отправить сообщение электронной почты с мобильных служб с помощью SendGrid]

    Узнайте, как добавить функциональные возможности электронной почты в мобильную службу, используя службу электронной почты SendGrid. В этом разделе показано, как добавить сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid.

-   [Планирование серверных заданий в мобильных службах][Планирование серверных заданий в мобильных службах]

    Узнайте, как использовать планировщик заданий мобильных служб для определения кода сценария на стороне сервера, который будет выполняться в соответствии с заданным расписанием.

-   [Справочник серверных скриптов мобильных служб][Справочник серверных скриптов мобильных служб]

    Справочные разделы по использованию серверных скриптов для выполнения задач на стороне сервера и интеграции с другими компонентами Azure, а также внешние ресурсы.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]

    Дополнительные сведения об использовании мобильных служб с .NET

<!-- Anchors.  Images. URLs. -->

  [Установите библиотеку клиента хранилища]: #install-storage-client
  [Обновите сценарий вставки для создания учетных данных SAS]: #update-scripts
  [Обновите клиентское приложение для захвата изображений]: #add-select-images
  [Передайте изображения для проверки приложения]: #test
  [Учетная запись хранения Azure]: /ru-ru/manage/services/storage/how-to-create-a-storage-account
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [Отправить сообщение электронной почты с мобильных служб с помощью SendGrid]: /ru-ru/develop/mobile/tutorials/send-email-with-sendgrid/
  [Планирование серверных заданий в мобильных службах]: /ru-ru/documentation/articles/mobile-services-schedule-recurring-tasks
  [Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
