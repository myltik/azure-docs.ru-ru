<properties pageTitle="Авторизация на стороне службы (Windows Phone) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как осуществить авторизацию пользователей в серверной части .NET мобильных служб Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Авторизация пользователей мобильных служб на стороне службы" authors="glenga" solutions="" manager="" editor="" />

# Авторизация пользователей мобильных служб на стороне службы

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Магазин Windows — C#">Магазин Windows — C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS">iOS</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/" title="Сервер .NET" class="current">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/"  title="Сервер JavaScript">Сервер JavaScript</a></div>

В этом разделе показано, как осуществить авторизацию пользователей, прошедших проверку подлинности, для доступа к данным в мобильных службах Azure из приложения Windows Phone. В этом учебнике вы добавите код в методы доступа к данным контроллера, которые используются для фильтрации запросов по userId пользователя, прошедшего проверку подлинности, удостоверяясь, что каждый пользователь может просматривать только свои собственные данные.

Этот учебник описывает быстрый запуск мобильных служб и основан на материале предыдущего учебника [Приступая к работе с аутентификацией]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с аутентификацией].  

## <a name="register-scripts"></a>Изменение методов доступа к данным

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)] 


## Тестирование приложения

1. Откройте в Visual Studio 2013 для Windows Phone проект, измененный при выполнении учебника [Приступая к работе с проверкой подлинности].

2. Нажмите клавишу F5 для запуска приложения, а затем выполните вход с использованием выбранного вами поставщика удостоверений. 

   	Обратите внимание, что в этом случае не возвращается никаких элементов, хотя в таблице TodoItem уже есть элементы из предыдущих учебников. Это вызвано тем, что предыдущие элементы были вставлены без столбца userId и теперь имеют значения NULL.

3. В приложении введите в текстовом поле текст и нажмите кнопку **Сохранить**.

   	![][3]

   	При этом в таблицу TodoItem мобильной службы вставляется текст и идентификатор пользователя. Так как новый элемент имеет правильное значение userId, он возвращается мобильной службой.

6. (Необязательно) При наличии дополнительных учетных записей можно проверить, что пользователи могут просмотреть только свои собственные данные, закрыв приложение (ALT+F4) и затем запустив его снова. Когда появится диалоговое окно учетных данных входа, введите другое имя входа и убедитесь, что элементы, введенные с использованием предыдущей учетной записи, не отображаются. 

## Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с проверкой подлинности. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.
  
* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.

<!-- Anchors. -->
[Регистрация серверных скриптов]: #register-scripts
[Дальнейшие действия]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push

[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

