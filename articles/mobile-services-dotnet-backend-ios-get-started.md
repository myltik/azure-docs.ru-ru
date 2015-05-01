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

В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. Мобильная служба использует .NET и Visual Studio для серверной бизнес-логики. Сведения о создании мобильной службы с серверной бизнес-логикой в JavaScript см. в подразделе [Серверная версия JavaScript] этого раздела.

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить [бесплатные мобильные службы, которые можно использовать и после окончания пробного периода](http://azure.microsoft.com/pricing/details/mobile-services/).  Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20target="_blank").

## <a name="create-new-service"> </a>Создание новой мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Загрузка мобильной службы и приложения на локальном компьютере

После создания мобильной службы загрузите проекты, которые можно выполнять локально.

1. Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска выберите **iOS** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения iOS**.

2. На компьютере под управлением Windows щелкните **Загрузить** в разделе **Загрузка и публикация службы в облако**. При этом загружается проект Visual Studio, реализующий мобильную службу. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

3. На компьютере Mac щелкните **Загрузить** в разделе **Загрузка и запуск приложения**. При этом загрузятся файлы проекта для примера по созданию приложения _Список задач_, подключенного к вашей мобильной службе, а также SDK мобильных служб для iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


## Запуск нового приложения для iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"></a>Дальнейшие действия

Здесь показано, как запустить новое клиентское приложение для мобильной службы, работающей в Azure. Перед тестированием приложения iOS с мобильной службой, работающей на локальном компьютере, необходимо настроить веб-сервер и брандмауэр, чтобы разрешить доступ с компьютера разработки iOS. Дополнительные сведения см. в разделе [Настройка локального веб-сервера для подключения к локальной мобильной службе](mobile-services-dotnet-backend-how-to-configure-iis-express.md).

Узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Добавление мобильных служб к существующему приложению]
  <br/>Дополнительные сведения о хранении данных и запросах к ним с помощью мобильных служб.

* [Начало работы с автономной синхронизацией данных]
  <br/>Узнайте, как обеспечить быстрый отклик и надежность приложений с помощью синхронизации автономных данных.

* [Добавление проверки подлинности к существующему приложению]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* [Добавление push-уведомлений для существующего приложения]
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Устранение неполадок серверной части .NET мобильных служб]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET.

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание новой мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Добавление мобильных служб к существующему приложению]: mobile-services-dotnet-backend-ios-get-started-data.md
[Начало работы с автономной синхронизацией данных]: mobile-services-ios-get-started-offline-data.md
[Добавление проверки подлинности к существующему приложению]: mobile-services-dotnet-backend-ios-get-started-users.md
[Добавление push-уведомлений для существующего приложения]: mobile-services-dotnet-backend-ios-get-started-push.md
[Устранение неполадок серверной части .NET мобильных служб]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия JavaScript]: mobile-services-ios-get-started.md


<!--HONumber=52-->