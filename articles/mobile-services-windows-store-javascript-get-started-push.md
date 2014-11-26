<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app (legacy push)." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Приступая к работе с push-уведомлениями в мобильных службах (принудительная отправка устаревшего типа)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Магазин Windows C#">Магазин Windows C#</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как Visual Studio 2013 позволяет использовать мобильные службы Azure для отправки push-уведомлений в приложение Магазина Windows. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Windows (WNS) непосредственно в Visual Studio. По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

> [WACOM.NOTE]Указания в этом разделе приведены с учетом использования *имеющихся* мобильных служб, которые *пока еще не были обновлены* до версии, поддерживающей интеграцию с центрами уведомлений. При создании *новой* мобильной службы интегрированные функции включаются автоматически. Информацию о работе с мобильными службами последних версий см. в разделе [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].
>
> Мобильные службы теперь интегрируются с центрами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и лучшее масштабирование. *Если это возможно, обновите имеющиеся мобильные службы, чтобы использовать центры уведомлений*. После обновления ознакомьтесь с этой версией раздела [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Регистрация приложения для push-уведомлений и конфигурация мобильных служб][Регистрация приложения для push-уведомлений и конфигурация мобильных служб]
2.  [Обновление созданного кода push-уведомлений][Обновление созданного кода push-уведомлений]
3.  [Вставка данных для получения уведомлений][Вставка данных для получения уведомлений]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение.

## <a name="register"></a><span class="short-header">Регистрация приложения</span>Добавление и настройка push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Откройте созданный файл кода push.register.js и проверьте код, который получает идентификатор установки и канал для устройства и вставляет эти данные в таблицу новых **каналов**.

    Эта таблица была создана в вашей мобильной службе с помощью мастера добавления push-уведомлений. Этот код гарантирует, что попытка регистрации устройства будет проводиться при активации приложения.

2.  В обозревателе сервера разверните раздел **Azure**, **Мобильные службы**, выберите имя своей службы, выберите **каналы**, а затем откройте файл insert.js.

    Этот файл, хранящийся в вашей мобильной службе, содержит код JavaScript, который выполняется, когда клиент отправляет запрос на регистрацию устройства, путем вставки данных в таблицу каналов.

    <div class="dev-callout"><b>Примечание.</b>
    <p>Исходная версия данного файла содержит код, который проверяет имеющуюся регистрацию устройства. Он также содержит код, который отправляет push-уведомление при добавлении новой регистрации в таблицу каналов. Код, который отправляет push-уведомление, может быть включен в любой зарегистрированный файл сценария. Расположение этого сценария зависит от того, что выступает триггером уведомления. Сценарии могут быть зарегистрированы по операции вставки, обновления, удаления или чтения в таблицы, в качестве запланированного задания или в качестве пользовательского интерфейса API. Дополнительную информацию см. в разделе <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Работа с серверными сценариями в мобильных службах</a>.</p>
</div>

3.  Нажмите клавишу F5, чтобы запустить приложение и проверить мгновенное получение уведомления от мобильной службы.

    Это уведомление было создано путем вставки в таблицу новых каналов строки, которая является регистрацией устройства.

Хотя созданный код без труда демонстрирует возможность уведомления при работе приложения, это, в целом, не является хорошим примером. Затем необходимо будет удалить код уведомления из таблицы каналов и заменить его (с некоторыми изменениями) в таблице TodoItem.

## <a name="update-scripts"></a><span class="short-header">Обновление кода</span>Обновление созданного кода push-уведомлений

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

## <a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении

1.  В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2.  В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

    ![][0]

    Обратите внимание, что после завершения вставки приложение получает push-уведомление из WNS.

    ![][1]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике демонстрируются основные функциональные возможности push-уведомлений, предоставляемые мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах, посвященных концентраторам уведомлений:

-   [Приступая к работе с центрами уведомлений][Приступая к работе с центрами уведомлений]
    Узнайте, как использовать центры уведомлений в приложении для Магазина Windows.

-   [Рассылка уведомлений подписчикам][Рассылка уведомлений подписчикам]
    Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

-   [Рассылка уведомлений пользователям][Рассылка уведомлений пользователям]
    Узнайте о том, как отправлять push-уведомления из мобильной службы конкретным пользователям на любом устройстве.

-   [Отправка пользователям уведомлений между различными платформами][Отправка пользователям уведомлений между различными платформами]
    Узнайте об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    Узнайте больше о регистрации и использовании серверных сценариев.

-   [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript]
    Узнайте больше о том, как использовать мобильные службы с HTML и JavaScript.

 
 


  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Регистрация приложения для push-уведомлений и конфигурация мобильных служб]: #register
  [Обновление созданного кода push-уведомлений]: #update-scripts
  [Вставка данных для получения уведомлений]: #test
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js/
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png
  [Приступая к работе с центрами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
  [Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
  [Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/
