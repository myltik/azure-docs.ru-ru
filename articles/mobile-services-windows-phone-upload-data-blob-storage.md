<properties pageTitle="Использование мобильных служб для передачи изображений в хранилище BLOB-объектов (Windows Phone) | Мобильные службы" metaKeywords="" description="Узнайте, как использовать мобильные службы для передачи изображений в хранилище BLOB-объектов Azure и получения доступа к изображениям из приложения Магазина Windows." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Передача изображений в хранилище Azure с помощью мобильных служб" authors="glenga" writer="glenga" />

# Передача изображений в хранилище Azure с помощью мобильных служб
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage" title="Windows Phone" class="current">Windows Phone</a>
</div>

В этом разделе показано, как использовать мобильные службы Azure, чтобы передавать и сохранять созданные изображения в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов (BLOB) эффективнее хранить в службе хранения BLOB-объектов Azure. 

Невозможно безопасно передать в клиентском приложении учетные данные, необходимые для безопасной передачи данных в службу хранения BLOB-объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. SAS, учетные данные с кратким сроком действия (в данном случае 5 минут), возвращаются безопасно в клиентское приложение мобильными службами. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы BLOB-объектов, являются общедоступными.

В этом учебнике вы добавите некоторые функциональные возможности к быстрому запуску мобильных служб для съемки фотографий и передачи изображений в Azure с помощью учетных данных SAS, созданных мобильными службами. Этот учебник поможет выполнить следующие базовые действия для обновления быстрого запуска мобильных служб, что позволит передавать изображения в службу хранения BLOB-объектов:

1. [Установите библиотеку клиента хранилища]
2. [Обновите сценарий вставки для создания учетных данных SAS]
3. [Обновите клиентское приложение для захвата изображений]
4. [Передайте изображения для проверки приложения]

Для данного учебника требуется следующее:

+ Microsoft Visual Studio 2012 Express для Windows 8 или более поздней версии
+ [Windows Phone SDK 8.0] и выше
+ Диспетчер пакетов NuGet, установленный для Microsoft Visual Studio.
+ [Учетная запись хранения Azure][How To Create a Storage Account]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 

##<a name="install-storage-client"></a>Установка клиента хранения для приложений Магазина Windows

Чтобы иметь возможность использовать учетные данные SAS для передачи изображений в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища для приложений Магазина Windows.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управление пакетами NuGet**.

2. В левой области выберите категорию **Интернет**, выберите **Включить предварительный выпуск**, выполните поиск пакета **WindowsAzure.Storage-Preview**, щелкните **Установить** для пакета **Хранилище Azure**, затем примите условия лицензионных соглашений. 

  	![][2]

  	Это приведет к добавлению клиентской библиотеки для служб хранилища Azure в проект.

Далее необходимо будет обновить приложение quickstart для захвата и сохранения изображений.

##<a name="update-scripts"></a>Обновление зарегистрированных скриптов вставки на портале управления

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы смогли безопасно передать изображения, интегрировав мобильную службу и службу BLOB-объектов, ознакомьтесь с некоторыми темами, посвященными серверным службам и интеграции:

+ [Отправить сообщение электронной почты с мобильных служб с помощью SendGrid]
 
  Узнайте, как добавить функциональные возможности электронной почты в мобильную службу, используя службу электронной почты SendGrid. В этом разделе показано, как добавить сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid.

+ [Планирование серверных заданий в мобильных службах]

  Узнайте, как использовать планировщик заданий мобильных служб для определения кода сценария на стороне сервера, который будет выполняться в соответствии с заданным расписанием.

+ [Справочник серверных скриптов мобильных служб]

  Справочные разделы по использованию серверных скриптов для выполнения задач на стороне сервера и интеграции с другими компонентами Azure, а также внешние ресурсы.
 
+ [Справочник принципов использования мобильных служб .NET]

  Дополнительные сведения об использовании мобильных служб с .NET
  
 
<!-- Anchors. -->
[Установите библиотеку клиента хранилища]: #install-storage-client
[Обновите клиентское приложение для захвата изображений]: #add-select-images
[Обновите сценарий вставки для создания учетных данных SAS]: #update-scripts
[Передайте изображения для проверки приложения]: #test
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
[Отправить сообщение электронной почты с мобильных служб с помощью SendGrid]: /ru-ru/develop/mobile/tutorials/send-email-with-sendgrid/
[Планирование серверных заданий в мобильных службах]: /ru-ru/develop/mobile/tutorials/schedule-backend-tasks/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started

[Портал управления Azure]: https://manage.windowsazure.com/
[Создание учетной записи хранения]: /ru-ru/manage/services/storage/how-to-create-a-storage-account
[Клиентская библиотека хранилища Azure для приложений Магазина]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/ru-ru/download/details.aspx?id=35471



