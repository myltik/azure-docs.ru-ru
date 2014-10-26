<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript][Серверная версия JavaScript] этого раздела.

Снимок экрана завершенного приложения приведен ниже:

![][]

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Скачивание мобильной службы на локальный компьютер

Теперь, когда мобильная служба создана, загрузите персональный проект мобильной службы, которая может выполняться на локальном компьютере или виртуальной машине.

1.  Щелкните только что созданную мобильную службу, затем на вкладке быстрого запуска в разделе **Выберите платформу** выберите **iOS** и разверните элемент **Создание нового приложения iOS**.

    ![][1]

2.  Если вы еще не сделали этого, скачайте и установите Visual Studio Professional 2013 или более поздней версии.

3.  Щелкните кнопку **Скачать** в разделе **Скачивание и публикация службы в облаке**.

    При этом загружается проект Visual Studio, реализующий мобильную службу. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

4.  Кроме того, скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запишите путь к нему.

## Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## Публикация мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Создание нового приложения для iOS

В этом разделе будет создано новое приложение на платформе iOS, которое будет подключено к вашей мобильной службе.

1.  В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2.  На вкладке быстрого запуска нажмите кнопку **iOS** в разделе **Выбор платформы**, а затем разверните пункт **Создать новое приложение на Android**.

3.  Если вы еще не сделали этого, загрузите и установите [Xcode][Xcode] v4.4 или более поздней версии.

4.  В разделе **Загрузка и запуск приложения** щелкните **Загрузить**.

    При этом загружается проект для примера приложения *To do list*, которое подключается к вашей мобильной службе, а также пакет Mobile Services SDK для iOS. Сохраните сжатый файл проекта на локальный компьютер и запомните путь к нему.

## Запуск нового приложения для iOS

[WACOM.INCLUDE [mobile-services-ios-run-app][mobile-services-ios-run-app]]

Здесь показано, как запустить новое клиентское приложение для мобильной службы, работающей в Azure. Перед тестированием приложения iOS с мобильной службой, работающей на локальном компьютере, необходимо настроить веб-сервер и брандмауэр, чтобы разрешить доступ с компьютера разработки iOS. Дополнительные сведения см. в разделе [Настройка локального веб-сервера для подключения к локальной мобильной службе][Настройка локального веб-сервера для подключения к локальной мобильной службе].

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с данными][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с синхронизацией автономных данных][Приступая к работе с синхронизацией автономных данных]

    Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Устранение неполадок серверной части в мобильных службах .NET][Устранение неполадок серверной части в мобильных службах .NET]

     Узнайте, как определять и исправлять неполадки, которые могут возникнуть в серверной части мобильных служб для .NET.



  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Серверная версия JavaScript]: /ru-ru/documentation/articles/mobile-services-ios-get-started
  []: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [mobile-services-ios-run-app]: ../includes/mobile-services-ios-run-app.md
  [Настройка локального веб-сервера для подключения к локальной мобильной службе]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Приступая к работе с синхронизацией автономных данных]: /ru-ru/documentation/articles/mobile-services-ios-get-started-offline-data
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [Устранение неполадок серверной части в мобильных службах .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
