<properties pageTitle="Начало работы с мобильными службами Azure для приложений iOS" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure. В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Для создания мобильной службы, которая позволяет писать серверную бизнес-логику на языке JavaScript, ознакомьтесь с разделом [Серверная версия базы данных JavaScript] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Скачивание мобильной службы на локальный компьютер

Теперь, когда мобильная служба создана, загрузите персональный проект мобильной службы, которая может выполняться на локальном компьютере или виртуальной машине.

1. Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска выберите **iOS** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения iOS**.

	![][1]

2. Если вы еще не сделали этого, загрузите и установите Visual Studio Professional 2013 или более поздней версии.

3. Щелкните **Загрузка** в разделе **Загрузить и опубликовать свою службу в облаке**.

	Это приведет к загрузке проекта Visual Studio, реализующего мобильную службу. Сохраните упакованный файл проекта на локальном компьютере и запомните, в каком месте он был сохранен.

4. Кроме того, скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запишите путь к нему.

## Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Создание нового приложения для iOS

В этом разделе будет создано новое приложение на платформе iOS, которое будет подключено к вашей мобильной службе.

1. На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.

2. На вкладке краткого руководства щелкните **iOS** в разделе **Выбор платформы** и разверните **Создать новое приложение iOS**.

3. Если это еще не сделано, загрузите и установите [Xcode] v4.4 или более позднюю версию.

4. В разделе **Загрузка и выполнение приложения** щелкните **Загрузка**.

  	Это приведет к загрузке проекта для примера приложения _To do list_, которое подключено к вашей мобильной службе, наряду с SDK мобильных служб iOS. Сохраните упакованный файл проекта на локальном компьютере и запомните, в каком месте он был сохранен.

## Запуск нового приложения для iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Здесь показано, как запустить новое клиентское приложение для мобильной службы, работающей в Azure. Перед тестированием приложения iOS с мобильной службой, работающей на локальном компьютере, необходимо настроить веб-сервер и брандмауэр, чтобы разрешить доступ с компьютера разработки iOS. Дополнительные сведения см. в разделе [Настройка локального веб-сервера для подключения к локальной мобильной службе](/ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Начало работы с автономной синхронизацией данных]
  <br/>Использование автономной синхронизации данных для повышения быстроты отклика и надежности приложения.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Приступая к работе с push-уведомлениями]
  <br/>Отправка очень простого push-уведомления в приложение.

* [Устранение неполадок сервера базы данных мобильных служб .NET]
  <br/> Диагностирование и устранение проблем, которые могут возникать при работе сервера базы данных мобильных служб .NET.

<!-- Anchors. -->
[Начало работы с мобильными службами]:#getting-started
[Создание новой мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Следующие шаги]:#next-steps

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
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
[Устранение неполадок сервера базы данных мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-ios-get-started
