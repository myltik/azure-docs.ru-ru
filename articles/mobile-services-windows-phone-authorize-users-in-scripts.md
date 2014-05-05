<properties pageTitle="Авторизация на стороне службы (Windows Phone) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как осуществить авторизацию пользователей в серверной части JavaScript мобильных служб Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Авторизация пользователей мобильных служб на стороне службы" authors="glenga" solutions="" manager="" editor="" />

# Авторизация пользователей мобильных служб на стороне службы

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML">HTML</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/" title="Сервер .NET">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a></div>	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать серверные скрипты для авторизации пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Windows Phone 8. В этом учебнике вы регистрируете скрипты с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.</p>
<p>Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8">Приступая к работе с проверкой подлинности</a>. Перед началом работы с учебником необходимо пройти задания учебника <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8">Приступая к работе с проверкой подлинности</a>.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">15:00</span></div>
</div> 

## <a name="register-scripts"></a>Регистрация серверных скриптов
Поскольку данное приложение быстрого запуска считывает и вставляет данные, необходимо зарегистрировать скрипты для этих операций в таблице TodoItem.

1. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните элемент **Скрипт** и выберите операцию **Вставить**.

   	![][2]

4. Замените имеющийся скрип следующей функцией и нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Этот скрипт добавляет в элемент значение userId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>При первом выполнении этого скрипта вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>userId</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows Phone ее следует отключить.</p>
    </div>


5. Повторите шаги 3 и 4, чтобы заменить существующую операцию **Read** следующей функцией:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Этот скрипт фильтрует возвращаемые объекты TodoItem, чтобы каждый пользователь получил только элементы, которые он вставил.

## Тестирование приложения

1. Откройте в Visual Studio 2012 Express для Windows Phone проект, измененный при завершении учебника [Приступая к работе с проверкой подлинности].

2. Нажмите клавишу F5 для запуска приложения, а затем выполните вход с использованием выбранного вами поставщика удостоверений. 

   	Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3. В приложении введите в текстовом поле текст и нажмите кнопку **Сохранить**.

   	![][3]

   	При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой.

5. В таблице **todoitem** в [портале управления][Azure Management Portal] нажмите кнопку **Обзор** и убедитесь, что каждый новый добавляемый элемент теперь имеет соответствующее значение userId.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

<!-- Anchors. -->
[Регистрация серверных скриптов]: #register-scripts
[Следующие шаги]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8

[Портал управления Azure]: https://manage.windowsazure.com/

