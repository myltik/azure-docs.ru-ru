<properties
	pageTitle="Начало работы с мобильными службами Azure для приложений iOS"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="2/11/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure.

В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы с серверной бизнес-логикой в . NET см. в подразделе [Серверная версия .NET] этого раздела.

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить [бесплатные мобильные службы, которые можно использовать и после окончания пробного периода](http://azure.microsoft.com/pricing/details/mobile-services/). Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20 target="_blank").

## <a name="create-new-service"> </a>Создание новой мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Создание нового приложения для iOS</h2>

Для создания нового приложения, подключенного к мобильной службе, можно использовать раздел быстрого запуска на портале управления:

1. На портале управления щелкните **Мобильные службы**, а затем выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **iOS** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение iOS**. Здесь показаны действия для создания приложения на платформе iOS, подключенного к вашей мобильной службе.

3. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**. При этом загрузятся файлы проекта для примера по созданию приложения _Список задач_, подключенного к вашей мобильной службе, а также SDK мобильных служб для iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запуск нового приложения для iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>На портале управления перейдите на вкладку <strong>ДАННЫЕ</strong>, а затем щелкните таблицу <strong>TodoItem</strong>. Это позволяет просматривать данные, добавленные в таблицу приложением.<p></li></ol></p>

## <a name="next-steps"> </a>Дальнейшие действия
Узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Добавление мобильных служб к существующему приложению]
	<br/>Дополнительные сведения о хранении данных и запросах к ним с помощью мобильных служб.

* [Начало работы с автономной синхронизацией данных]
	<br/>Узнайте, как обеспечить быстрый отклик и надежность приложений с помощью синхронизации автономных данных.

* [Добавление проверки подлинности к существующему приложению]
	<br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* [Добавление push-уведомлений для существующего приложения]
	<br/>Сведения об отправке в приложение простейших push-уведомлений.


<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание новой мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Добавление мобильных служб к существующему приложению]: mobile-services-dotnet-backend-ios-get-started-data.md
[Начало работы с автономной синхронизацией данных]: mobile-services-ios-get-started-offline-data.md
[Добавление проверки подлинности к существующему приложению]: mobile-services-dotnet-backend-ios-get-started-users.md
[Добавление push-уведомлений для существующего приложения]: mobile-services-dotnet-backend-ios-get-started-push.md


[Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия .NET]: mobile-services-dotnet-backend-ios-get-started.md

<!--HONumber=52-->