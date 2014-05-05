<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Приступая к работе с проверкой подлинности (iOS)" pageTitle="Приступая к работе с проверкой подлинности (iOS) | Центр разработчиков для мобильных устройств" metaKeywords="Регистрация приложения Azure, проверка подлинности Azure, проверка подлинности приложения, проверка подлинности мобильных служб, мобильные службы iOS" description="Узнайте, как использовать мобильные службы для проверки подлинности пользователей приложения iOS с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и Майкрософт." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с проверкой подлинности в мобильных службах" authors="" solutions="" manager="" editor="" />

# Приступая к работе с аутентификацией в мобильных службах
<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-users" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-users/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении для iOS.  В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, для этого используется поставщик удостоверений, который поддерживается мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

Для изучения этого учебника необходимы XCode версии 4.5 и iOS версии 5.0 или более поздних версий. 

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>В Xcode откройте проект, созданный после завершения учебника <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started">Приступая к работе с мобильными службами</a>.</p></li> 
<li><p>Нажмите кнопку <strong>Выполнить</strong> для построения проекта и запуска приложения в эмуляторе iPhone; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано).<p> 
   
   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Портал управления Azure]: https://manage.windowsazure.com/

