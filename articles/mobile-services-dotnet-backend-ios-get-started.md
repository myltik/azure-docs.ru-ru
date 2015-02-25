<properties pageTitle="Начало работы с мобильными службами Azure для приложений iOS" description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений iOS." services="" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий.

> [AZURE.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20target="_blank").

## <a name="create-new-service"> </a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Скачивание мобильной службы на локальный компьютер

Теперь, когда мобильная служба создана, загрузите персональный проект мобильной службы, которая может выполняться на локальном компьютере или виртуальной машине.

1. Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска выберите **iOS** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения iOS**.

	![][1]

2. Если вы еще не сделали этого, загрузите и установите Visual Studio Professional 2013 или более поздней версии.

3. Щелкните **Загрузить** в разделе **Загрузка и публикация службы в облаке**.

	При этом загружается проект Visual Studio, реализующий мобильную службу. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

4. Кроме того, скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запишите путь к нему.

## Тестирование мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Создание нового приложения для iOS

В этом разделе будет создано новое приложение на платформе iOS, которое будет подключено к вашей мобильной службе.

1. В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **iOS** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение на Android**.

3. Если вы еще не сделали этого, загрузите и установите [Xcode] 4.4 или более поздней версии.

4. В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

  	При этом загружается проект для примера приложения _To do list_, которое подключается к вашей мобильной службе, а также пакет Mobile Services SDK для iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запуск нового приложения для iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Здесь показано, как запустить новое клиентское приложение для мобильной службы, работающей в Azure. Перед тестированием приложения iOS с мобильной службой, работающей на локальном компьютере, необходимо настроить веб-сервер и брандмауэр, чтобы разрешить доступ с компьютера разработки iOS. Дополнительные сведения см. в разделе [Настройка локального веб-сервера для подключения к локальной мобильной службе](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним с помощью мобильных служб.

* [Приступая к работе с автономной синхронизацией данных]
  <br/>Узнайте, как обеспечить быстрый отклик и надежность приложений с помощью синхронизации автономных данных.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* [Приступая к работе с push-уведомлениями]
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Устранение неполадок серверной части .NET мобильных служб]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET.

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
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
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Приступая к работе с автономной синхронизацией данных]: /ru-ru/documentation/articles/mobile-services-ios-get-started-offline-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
[Устранение неполадок серверной части .NET мобильных служб]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-ios-get-started


<!--HONumber=42-->
