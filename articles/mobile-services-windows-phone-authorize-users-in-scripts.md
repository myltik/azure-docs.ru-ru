<properties pageTitle="Авторизация с помощью службы (Windows Phone) | Центр мобильных разработок" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Авторизация пользователей мобильных служб на стороне службы

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать серверные скрипты для авторизации пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Windows Phone 8. В этом учебнике вы регистрируете скрипты с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.</p>
<p>Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8">Приступая к работе с проверкой подлинности</a>. Перед началом работы с учебником необходимо пройти задания учебника <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8">Приступая к работе с проверкой подлинности</a>.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">15:00</span></div>
</div> 

## <a name="register-scripts"></a>Регистрация сценариев
Поскольку данное приложение быстрого запуска считывает и вставляет данные, необходимо зарегистрировать скрипты для этих операций в таблице TodoItem.

1. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные**, затем щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните **Скрипт**, затем выберите операцию **Вставить**.

   	![][2]

4. Замените существующий скрипт следующей функцией, затем нажмите кнопку **Сохранить**.

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

1. В Visual Studio 2012 Express для Windows Phone откройте проект, над которым вы работали при прохождении учебника [Приступая к работе с проверкой подлинности].

2. Нажмите клавишу F5 для запуска приложения, а затем выполните вход с использованием выбранного вами поставщика удостоверений. 

   	Обратите внимание, что несмотря на наличие элементов, уже сохраненных в таблице TodoItem после прохождения предыдущих учебников, возврат элементов не происходит. Причина этого состоит в том, что предыдущие элементы были вставлены без столбца идентификатора пользователя и теперь имеют значения NULL.

3. В приложении введите текст в текстовом поле и щелкните **Сохранить**.

   	![][3]

   	Это приведет к вставке текста и идентификатора пользователя в таблицу TodoItem в мобильной службе. Новый элемент имеет правильное значение идентификатора пользователя, поэтому происходит его возврат мобильной службой.

5. Вернувшись к таблице **todoitem** на [Портале управления][Портала управления Azure], щелкните **Обзор** и проверьте, имеет ли каждый вновь добавленный элемент соответствующее значение идентификатора пользователя.

## Дальнейшие действия

На этом завершаются учебники, демонстрирующие основы работы с проверкой подлинности. Рассмотрите возможность лучше ознакомиться со следующими темами, касающимися мобильных служб:

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник серверных скриптов мобильных служб]
  <br/>Регистрация и использование серверных скриптов.

<!-- Anchors. -->
[Регистрация серверных скриптов]: #register-scripts
[Дальнейшие действия]:#next-steps

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
