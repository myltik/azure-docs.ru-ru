<properties 
	pageTitle="Авторизация с помощью службы (Windows Phone) | Центр мобильных разработок" 
	description="Узнайте, как осуществить авторизацию пользователей в серверной части JavaScript мобильных служб Azure." 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# Авторизация пользователей мобильных служб на стороне службы

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать серверные сценарии для авторизации пользователей, прошедших аутентификацию, для доступа к данным в мобильных службах Azure из приложения для Windows Phone 8. В этом учебнике вы зарегистрируете скрипты с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.</p>
<p>Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8">Приступая к работе с аутентификацией</a>. Перед началом работы с учебником необходимо пройти задания учебника <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8">Приступая к работе с аутентификацией</a>.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">15:00</span></div>
</div> 

## <a name="register-scripts"></a>Регистрация скриптов
Поскольку данное приложение быстрого запуска считывает и вставляет данные, необходимо зарегистрировать скрипты для этих операций в таблице TodoItem.

1. Войдя на [портал управления Azure], щелкните элемент **Мобильные службы** и выберите нужное приложение. 

   	![][0]

2. На вкладке **Данные** щелкните таблицу **TodoItem**.

   	![][1]

3. Выберите элемент **Скрипт**, а затем - операцию **Вставить**.

   	![][2]

4. Замените существующий скрипт указанной ниже функцией и нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Этот скрипт добавляет в элемент значение userId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem. 

    > [AZURE.NOTE] При первом выполнении этого сценария вставки должна быть включена динамическая схема. После включения динамической схемы мобильные службы автоматически добавляют столбец **userId** в таблицу **TodoItem** при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows Phone ее следует отключить.


5. Повторите шаги 3 и 4, заменив существующую операцию **Read** такой функцией:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Этот скрипт фильтрует возвращаемые объекты TodoItem, чтобы каждый пользователь получил только элементы, которые он вставил.

## Тестирование приложения

1. В Visual Studio 2012 Express для Windows Phone откройте проект, который вы изменили при прохождении учебника [Приступая к работе с проверкой подлинности].

2. Нажмите клавишу F5, чтобы запустить приложение, а затем выполните вход с использованием выбранного поставщика удостоверений. 

   	Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3. В приложении введите в текстовом поле текст и нажмите кнопку **Сохранить**.

   	![][3]

   	При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой.

5. Вернувшись к таблице **todoitem** на [портале управления][портал управления Azure], нажмите кнопку **Обзор** и проверьте, имеет ли каждый вновь добавленный элемент соответствующее значение идентификатора пользователя.

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним с помощью мобильных служб.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительная информация о регистрации и использовании серверных сценариев.

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
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8

[Портал управления Azure]: https://manage.windowsazure.com/



<!--HONumber=42-->
