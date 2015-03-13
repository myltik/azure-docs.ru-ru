<properties 
	pageTitle="Использование мобильных служб для отправки изображений в хранилище больших двоичных объектов (Windows Phone) | Мобильные службы" 
	description="Узнайте, как использовать мобильные службы для передачи образов в хранилище больших двоичных объектов Azure." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	Writer="wesmc" 
	services="mobile-services, storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/06/2014" 
	ms.author="wesmc"/>

# Отправка образов в хранилище Azure с помощью мобильных служб

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

В этом разделе показано, как использовать мобильные службы Azure для передачи и сохранения созданных пользователем изображений в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов (BLOB) более эффективно хранить в службе хранения BLOB-объектов Azure. 

Невозможно безопасно передать в клиентском приложении учетные данные, необходимые для безопасной передачи данных в службу хранения BLOB-объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. Подпись общего доступа является типом учетных данных с быстрым окончанием срока действия,&mdash;в данном случае это 5 минут. Она безопасно возвращается мобильными службами в клиентское приложение. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы BLOB-объектов, являются общедоступными.

В ходе выполнения этого учебника в [проект приложения GetStartedWithData](/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/) добавляются функциональные возможности для съемки фотографий и их передачи в Azure с помощью SAS, созданных мобильными службами. Этот учебник поможет выполнить следующие базовые действия для обновления примера приложения TodoList, что позволит передавать изображения в службу хранилища BLOB-объектов:

1. [Установка библиотеки клиента хранилища]
2. [Обновление скриптов вставки для создания учетных данных SAS]
3. [Обновление клиентского приложения для захвата изображений]
4. [Тестирование передачи изображений с помощью приложения]

Для работы с данным учебником требуется следующее:

+ Microsoft Visual Studio 2012 Express для Windows 8 или более поздней версии.
+ [Windows Phone SDK 8.0] и выше
+ Диспетчер пакетов NuGet, установленный для Microsoft Visual Studio.
+ [Учетная запись хранилища Azure][Создание учетной записи хранилища]
+ Пройдите учебник [Добавление мобильных служб к существующему приложению](/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/)  


##<a name="install-storage-client"></a>Установка клиента хранения для приложений Windows Phone

Чтобы иметь возможность использовать учетные данные SAS для передачи образов в хранилище больших двоичных объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента службы хранилища для приложений для Windows Phone.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2. В левой области выберите категорию **Интернет**, выберите **Включить предварительный выпуск**, выполните поиск пакета **WindowsAzure.Storage-Preview**, щелкните **Установить** для пакета **Хранилище Azure**, затем примите условия лицензионных соглашений. 

  	![][2]

  	Это приведет к добавлению в проект библиотеки клиента для служб хранилища Azure.

Далее необходимо будет обновить приложение quickstart для захвата и сохранения изображений.

##<a name="update-scripts"></a>Обновление зарегистрированного скрипта вставки на портале управления


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Для добавления новых свойств к объекту TodoItem необходимо включить динамическую схему в мобильной службе. После включения динамической схемы в таблицу TodoItem автоматически добавляются новые столбцы, которые соответствуют этим новым свойствам.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы смогли безопасно передать изображения, интегрировав мобильную службу и службу BLOB-объектов, ознакомьтесь с некоторыми темами, посвященными серверным службам и интеграции:

+ [Отправка сообщений электронной почты из мобильных служб с помощью SendGrid]
 
  Узнайте, как добавить функциональные возможности электронной почты в мобильную службу, используя службу электронной почты SendGrid. В этом разделе показано, как добавить сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid.

+ [Планирование серверных заданий в мобильных службах]

  Узнайте, как использовать планировщик заданий мобильных служб для определения кода сценария на стороне сервера, который будет выполняться в соответствии с заданным расписанием.

+ [Справочник серверных скриптов мобильных служб]

  Справочные разделы по использованию серверных скриптов для выполнения задач на стороне сервера и интеграции с другими компонентами Azure, а также внешние ресурсы.
 
+ [Справочник по принципам использования мобильных служб .NET]

  Дополнительные сведения об использовании мобильных служб с .NET
  
 
<!-- Anchors. -->
[Установка библиотеки клиента хранилища]: #install-storage-client
[Обновление клиентского приложения для захвата изображений]: #add-select-images
[Обновление скриптов вставки для создания учетных данных SAS]: #update-scripts
[Тестирование передачи изображений с помощью приложения]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->


[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


[5]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png
[6]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-wp8.png
[7]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-wp8.png
[8]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-save-wp8.png
[9]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-final-wp8.png

[11]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png

<!-- URLs. -->
[Отправка сообщений электронной почты из мобильных служб с помощью SendGrid]: /ru-ru/develop/mobile/tutorials/send-email-with-sendgrid/
[Планирование серверных заданий в мобильных службах]: /ru-ru/develop/mobile/tutorials/schedule-backend-tasks/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started

[Портал управления Azure]: https://manage.windowsazure.com/
[Создание учетной записи хранилища]: /ru-ru/manage/services/storage/how-to-create-a-storage-account
[Библиотека клиентского хранилища Azure для приложений Магазина]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=35471




<!--HONumber=42-->
