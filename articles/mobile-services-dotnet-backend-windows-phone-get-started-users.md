<properties pageTitle="Приступая к работе с аутентификацией (Windows Phone) | Центр разработчиков для мобильных устройств" metaKeywords="проверка подлинности, Facebook, Google, Twitter, учетная запись Майкрософт, вход" description="Узнайте, как использовать мобильные службы для аутентификации учетных данных пользователей приложения Windows Phone с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Приступая к работе с аутентификацией в мобильных службах" authors="Glenn Gailey" solutions="" manager="" editor="" />

# Приступая к работе с аутентификацией в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Магазин Windows — C#">Магазин Windows — C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a><!--<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/" title="Серверная версия .NET" class="current">Серверная версия.NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users/"  title="Серверная версия JavaScript">Серверная версия JavaScript</a></div>

В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure из приложения Windows Phone. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

>[WACOM.NOTE]В этом учебнике показан базовый метод, предоставляемый мобильными службами для аутентификации учетных данных пользователей с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. В то же время этот метод также требует от пользователей осуществлять вход каждый раз при запуске приложения. Сведения о том, как вместо этого использовать Live Connect для предоставления единого входа в приложении Windows Phone, см. в разделе [Единый вход для приложений Windows Phone с использованием Live Connect].

##<a name="register"></a> Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>В Visual Studio откройте файл Web.config для проекта мобильной службы и в разделе appSettings задайте идентификатор приложения и общие секретные значения, полученные от поставщика удостоверений.</p>
<p>Эти параметры используются при локальной разработке. После публикации проекта мобильной службы в Azure эти параметры переопределяются значениями, заданными на портале.</p></li>
</ol>

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="3">
<li><p>В Visual Studio 2013 откройте проект, созданный в ходе работы с учебником <a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/">Приступая к работе с мобильными службами</a>.</p></li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]: #next-steps


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений Windows Phone с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-phone-single-sign-on
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
[JavaScript и HTML]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложения для магазина Windows для проверки подлинности Microsoft]: /ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication

