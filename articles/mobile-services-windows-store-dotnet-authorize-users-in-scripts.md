<properties pageTitle="Авторизация пользователей на стороне службы (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как осуществить авторизацию пользователей в серверной части JavaScript мобильных служб Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Авторизация пользователей мобильных служб на стороне службы

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как осуществить авторизацию пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Магазина Windows. В этом учебнике вы регистрируете сценарии с помощью мобильных служб для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.</p>
<p>Можно смотреть видеоверсию данного учебника, щелкнув клип справа.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">13:52:00</span></div>
</div>

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с проверкой подлинности]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с аутентификацией].  

## <a name="register-scripts"></a>Регистрация сценариев
Поскольку данное приложение быстрого запуска считывает и вставляет данные, необходимо зарегистрировать сценарии для этих операций в таблице TodoItem.

1. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные**, затем щелкните таблицу **TodoItem**.

   	![][1]

3. Щелкните **Скрипт**, затем выберите операцию **Вставить**.

   	![][2]

4. Замените существующий сценарий следующей функцией, затем нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Этот скрипт добавляет в элемент значение userId, которое является идентификатором пользователя, прошедшего проверку подлинности, до его вставки в таблицу TodoItem. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>При первом выполнении этого сценария вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>userId</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows ее следует отключить.</p>
    </div>


5. Повторите шаги 3 и 4, чтобы заменить существующую операцию **Read** следующей функцией:

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Этот сценарий фильтрует возвращаемые объекты TodoItem, чтобы каждый пользователь получил только элементы, которые он вставил.

## Тестирование приложения

1. Откройте в Visual Studio 2012 Express для Windows 8 проект, измененный при завершении учебника [Приступая к работе с проверкой подлинности].

2. Нажмите клавишу F5 для запуска приложения, а затем выполните вход с использованием выбранного вами поставщика удостоверений. 

   	Обратите внимание, что несмотря на наличие элементов, уже сохраненных в таблице TodoItem после прохождения предыдущих учебников, возврат элементов не происходит. Причина этого состоит в том, что предыдущие элементы были вставлены без столбца идентификатора пользователя и теперь имеют значения NULL.

3. В приложении введите текст в окне **Вставка TodoItem**, затем щелкните **Сохранить**.

   	![][3]

   	Это приведет к вставке текста и идентификатора пользователя в таблицу TodoItem в мобильной службе. Новый элемент имеет правильное значение идентификатора пользователя, поэтому происходят его возврат мобильной службой и отображение во втором столбце.

5. В таблице **todoitem** в [портале управления][Azure Management Portal] нажмите кнопку **Обзор** и убедитесь, что теперь каждый новый добавляемый элемент имеет соответствующее значение userId.

6. (Необязательно) При наличии дополнительных учетных записей можно проверить, что пользователи могут просмотреть только свои собственные данные, закрыв приложение (ALT+F4) и затем запустив его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются. 

## Дальнейшие действия

На этом завершаются учебники, демонстрирующие основы работы с проверкой подлинности. Рассмотрите возможность лучше ознакомиться со следующими темами, касающимися мобильных служб:

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник серверных сценариев мобильных служб]
  <br/>Регистрация и использование серверных сценариев.
  
* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.

<!-- Anchors. -->
[Регистрация серверных сценариев]: #register-scripts
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Push-уведомления Windows и Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript и HTML]: mobile-services-win8-javascript/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
