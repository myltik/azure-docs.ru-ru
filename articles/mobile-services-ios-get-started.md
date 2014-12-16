<properties pageTitle="Начало работы с мобильными службами Azure для приложений iOS" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="hero-article" ms.date="10/10/2014" ms.author="krisragh" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом учебнике показано, как добавить облачную серверную службу в приложение iOS с помощью мобильных служб Azure.</p>
<p>Если вы предпочитаете смотреть видео, то в клипе справа приведены те же действия, что и в учебнике.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">09:38:00</span></div>
</div>

В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых платформой .NET, см. в разделе [о серверной версии .NET].

Снимок экрана завершенного приложения приведен ниже:

![][0]

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Создание нового приложения для iOS</h2>

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе.

В этом разделе будет создано новое приложение на платформе iOS, которое будет подключено к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.

2. На вкладке краткого руководства щелкните **iOS** в разделе **Выбор платформы** и разверните **Создать новое приложение iOS**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на платформе iOS, подключенного к вашей мобильной службе.

  	![][7]

3. Если это еще не сделано, загрузите и установите [Xcode] v4.4 или более позднюю версию.

4. Щелкните **Создание таблицы TodoItems**, чтобы создать таблицу для хранения данных приложения.

5. В разделе **Загрузка и выполнение приложения** щелкните **Загрузка**.

  	Это приведет к загрузке проекта для примера приложения _To do list_, которое подключено к вашей мобильной службе, наряду с SDK мобильных служб iOS. Сохраните упакованный файл проекта на локальном компьютере и запомните, в каком месте он был сохранен.

## Запуск нового приложения для iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>На портале управления перейдите на вкладку <strong>Данные</strong>, а затем щелкните таблицу <strong>TodoItems</strong>.<p></li></ol>

![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

Это позволяет просматривать данные, добавленные в таблицу приложением.</p>

![](./media/mobile-services-ios-get-started/mobile-data-browse.png)


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


<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users/
[Начало работы с автономной синхронизацией данных]: /ru-ru/documentation/articles/mobile-services-ios-get-started-offline-data
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Серверная версия .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started
