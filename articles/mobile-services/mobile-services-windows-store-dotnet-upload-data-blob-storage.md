<properties 
	pageTitle="Использование мобильных служб для загрузки изображений в хранилище больших двоичных объектов (Магазин Windows) | Мобильные службы" 
	description="Узнайте, как использовать мобильные службы для передачи изображений в хранилище больших двоичных объектов Azure и получения доступа к изображениям из приложения магазина Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="glenga"/>

# Отправка образов в хранилище Azure с помощью мобильных служб

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

В этом разделе показано, как использовать мобильные службы Azure, чтобы передавать и сохранять созданные изображения в хранилище Azure через свое приложение. Для хранения данных мобильные службы используют базу данных SQL. Однако данные больших двоичных объектов (BLOB) более эффективно хранить в службе хранения BLOB-объектов Azure.

Невозможно безопасно передать в клиентском приложении учетные данные, необходимые для безопасной передачи данных в службу хранения BLOB-объектов. Вместо этого необходимо сохранить эти учетные данные в мобильной службе и использовать их для создания подписи общего доступа (SAS), которая используется для передачи нового изображения. SAS, учетные данные с кратким сроком действия (в данном случае 5 минут), возвращаются безопасно в клиентское приложение мобильными службами. Затем приложение использует эти временные учетные данные для передачи изображения. В этом примере файлы, загружаемые из службы BLOB-объектов, являются общедоступными.

В этом учебнике вы добавите некоторые функциональные возможности к учебному приложению мобильных служб для съемки фотографий и передачи изображений в Azure с помощью учетных данных SAS, созданных мобильными службами.

##Предварительные требования

Для работы с данным учебником требуется следующее:

+ Microsoft Visual Studio 2012 Express для Windows 8 или более поздней версии.
+ [Учетная запись хранения Azure](../storage-create-storage-account.md)
+ Камера или другое устройство для съемки, подключенное к компьютеру.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами].

##<a name="install-storage-client"></a>Установка клиента хранилища для приложений Магазина Windows

Чтобы иметь возможность использовать учетные данные SAS для передачи изображений в хранилище BLOB-объектов, необходимо добавить пакет NuGet, который устанавливает библиотеку клиента хранилища для приложений Магазина Windows.

1. В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши имя проекта, затем выберите **Управлять пакетами NuGet**.

2. В левой области выберите категорию **В сети**, найдите пакет `WindowsAzure.Storage`, щелкните **Установить** для пакета **Хранилище Azure**, а затем примите условия лицензионных соглашений.

  	![][2]

  	Это приведет к добавлению в проект библиотеки клиента для служб хранилища Azure.

Далее необходимо будет обновить приложение quickstart для захвата и сохранения изображений.

##<a name="update-scripts"></a>Обновление зарегистрированных скриптов вставки на портале управления

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы смогли безопасно передать изображения, интегрировав мобильную службу и службу BLOB-объектов, ознакомьтесь с некоторыми темами, посвященными серверным службам и интеграции:

+ [Отправка сообщений электронной почты из мобильных служб с помощью SendGrid]
 
  Узнайте, как добавить функциональные возможности электронной почты в мобильную службу, используя службу электронной почты SendGrid. В этом разделе показано, как добавить сценарии на стороне сервера для отправки сообщений электронной почты с помощью SendGrid.

+ [Планирование серверных заданий в мобильных службах]

  Узнайте, как использовать планировщик заданий мобильных служб для определения кода сценария на стороне сервера, который будет выполняться в соответствии с заданным расписанием.

+ [Справочник серверных скриптов мобильных служб]

  Справочные разделы по использованию серверных скриптов для выполнения задач на стороне сервера и интеграции с другими компонентами Azure, а также внешние ресурсы.
 
+ [Справочник принципов использования мобильных служб .NET]

  Дополнительные сведения об использовании мобильных служб с .NET
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Отправка сообщений электронной почты из мобильных служб с помощью SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Планирование серверных заданий в мобильных службах]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Справочник серверных скриптов мобильных служб]: mobile-services-how-to-use-server-scripts.md
[Приступая к работе с мобильными службами]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Справочник принципов использования мобильных служб .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=July15_HO3-->