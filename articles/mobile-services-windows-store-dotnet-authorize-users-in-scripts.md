<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Авторизация пользователей мобильных служб на стороне службы

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Магазин Windows &mdash; C#" class="current">Магазин Windows &mdash; C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash;JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как осуществить авторизацию пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Магазина Windows. В этом учебнике вы зарегистрируете скрипты с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.</p>
<p>Можно смотреть видеоверсию данного учебника, щелкнув клип справа.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">13:52:00</span></div>

</div>

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

## <a name="register-scripts"></a>Регистрация сценариев

Поскольку данное приложение быстрого запуска считывает и вставляет данные, необходимо зарегистрировать скрипты для этих операций в таблице TodoItem.

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

    ![][]

2.  Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

    ![][1]

3.  Щелкните **Сценарий** и выберите операцию **Вставить**.

    ![][2]

4.  Замените имеющийся сценарий следующей функцией и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Этот скрипт добавляет в элемент значение userId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem.

    <div class="dev-callout"><b>Примечание.</b>
<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>userId</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows ее следует отключить.</p>
</div>

5.  Повторите шаги 3 и 4, чтобы заменить существующую операцию **Read** следующей функцией:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Этот скрипт фильтрует возвращаемые объекты TodoItem, чтобы каждый пользователь получил только элементы, которые он вставил.

## Тестирование приложения

1.  Откройте в Visual Studio 2012 Express для Windows 8 проект, измененный во время работы с учебником [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

2.  Нажмите клавишу F5, чтобы запустить приложение, а затем выполните вход с использованием выбранного поставщика удостоверений.

    Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3.  В приложении введите текст в поле **Insert a TodoItem** (Вставка TodoItem), а затем нажмите кнопку **Сохранить**.

    ![][3]

    При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой и отображается во втором столбце.

4.  В таблице **todoitem** на [портале управления][портал управления Azure] нажмите кнопку **Обзор** и убедитесь, что каждый новый добавляемый элемент теперь имеет соответствующее значение userId.

5.  (Необязательно.) При наличии дополнительных учетных записей вы можете проверить, что пользователи могут просмотреть только свои собственные данные. Для этого можно закрыть приложение (Alt+F4) и затем запустить его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
    <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    <br/>Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    <br/>Узнайте больше о регистрации и использовании серверных сценариев.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
    <br/>Дополнительные сведения об использовании мобильных служб в .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Магазин Windows — C#]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Магазин Windows — C#"
  [Магазин Windows —JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Магазин Windows — JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android"
  [HTML]: /ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/ "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/ "Серверная часть JavaScript"
  [Просмотр учебника]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
