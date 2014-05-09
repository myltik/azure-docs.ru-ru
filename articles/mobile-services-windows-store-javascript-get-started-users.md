<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Приступая к работе с аутентификацией" pageTitle="Приступая к работе с аутентификацией (JavaScript) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Использование мобильных служб для проверки подлинности пользователей приложения Магазина Windows JavaScript с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и Microsoft." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/" services="" documentationCenter="Mobile" title="Приступая к работе с аутентификацией в мобильных службах" authors="" solutions="" manager="" editor="" />

# Приступая к работе с проверкой подлинности в мобильных службах
<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/" title="Сервер .NET">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a></div>

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении.  В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска; для этого используется поставщик удостоверений, который поддерживается мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

<div class="dev-callout"><b>Примечание.</b>
	<p>В этом учебнике показан базовый метод, предоставляемый мобильными службами для проверки подлинности пользователей с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. В то же время этот метод также требует от пользователей осуществлять вход каждый раз при запуске приложения. Сведения о том, как вместо этого использовать Live Connect для предоставления единого входа в приложении Магазина Windows, см. в разделе <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-js">Единый вход для приложений Магазина Windows с использованием Live Connect</a>.</p>
</div>

##<a name="register"></a> Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Необязательно) Выполните шаги, указанные в пункте <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Регистрация пакета приложения Магазина Windows для проверки подлинности Майкрософт</a>.</p>

    <div class="dev-callout"><b>Примечание.</b>
	<p>Этот шаг является необязательным, так как он применяется только к поставщику входа учетной записи Майкрософт. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Выполните этот шаг, если будет использоваться поставщик удостоверений учетной записи Майкрософт.</p>
    </div>
</li>
</ol>
Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

##<a name="permissions"></a> Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>В Visual Studio 2012 Express для Windows 8 откройте проект, созданный после завершения учебника <a href="/ru-ru/develop/mobile/tutorials/get-started/">Приступая к работе с мобильными службами</a>.</p></li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a> Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. 


<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Портал управления Azure]: https://manage.windowsazure.com/
[Регистрация пакета приложения Магазина Windows для проверки подлинности Майкрософт]: /ru-ru/develop/mobile/how-to-guides/register-windows-store-app-package

