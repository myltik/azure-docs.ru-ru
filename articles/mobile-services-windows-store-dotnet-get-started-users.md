<properties pageTitle="Приступая к работе с проверкой подлинности (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="проверка подлинности, Facebook, Google, Twitter, учетная запись Майкрософт, имя входа" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey" />

# Добавление проверки подлинности к приложению мобильных служб 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.</p>
<p>Можно смотреть видеоверсию данного учебника, щелкнув клип справа.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:04:00</span></div>
</div> 

>[WACOM.NOTE]В настоящее время проверка подлинности для приложений из Магазина Windows Phone 8.1 не поддерживается, если используется клиентская библиотека JavaScript для мобильных служб. Если у вас универсальное приложение для Windows, использующее клиент JavaScript, то выполнить проверку подлинности пользователей в Windows Phone сейчас будет невозможно.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]
5. [Хранение маркеров проверки подлинности в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

>[WACOM.NOTE]В этом учебнике показан поток проверки подлинности, управляемый мобильными службами с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. Сведения о том, как вместо этого использовать Live Connect для управляемой клиентом проверки подлинности и предоставления единого входа в приложении Windows Phone, см. в разделе [Единый вход для приложений магазина Windows с использованием Live Connect]. При использовании управляемой клиентом проверки подлинности приложение имеет доступ к дополнительным данным пользователя, сохраняемым поставщиком удостоверений. Эти же данные можно получить в мобильной службе, вызвав функцию **user.getIdentities()** в серверном скрипте. Дополнительные сведения см. в [этой записи блога](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Необязательно) Выполните шаги, указанные в пункте <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Регистрация пакета приложения Магазина Windows для проверки подлинности Майкрософт</a>.</p>

    <div class="dev-callout"><b>Примечание.</b>
	<p>Этот шаг является необязательным, так как он применяется только к поставщику входа учетной записи Майкрософт. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Выполните этот шаг, если будет использоваться поставщик удостоверений учетной записи Майкрософт.</p>
    </div>
</li>
</ol>

##<a name="permissions"></a> Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>В Visual Studio 2012 Express для Windows 8 откройте проект, созданный в ходе работы с учебником <a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started">Приступая к работе с мобильными службами</a>.</p></li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a> Добавление проверки подлинности к приложению

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>Хранение маркера авторизации в клиенте

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение идентификатора пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Хранение маркеров проверки подлинности в клиенте]: #tokens
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript и HTML]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложения для магазина Windows для проверки подлинности Microsoft]: /ru-ru/develop/mobile/how-to-guides/register-windows-store-app-package
