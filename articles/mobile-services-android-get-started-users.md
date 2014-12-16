<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Начало работы по проверке подлинности (Android) | Центр мобильных разработок" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal" />

# Добавление проверки подлинности к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">10:42</span></div>
</div> 

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]
4. [Хранение маркеров проверки подлинности в клиенте]
5. [Обновление маркеров с истекшим сроком действия]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами]. 

>[AZURE.NOTE] Если требуется просмотреть исходный код завершенного приложения, см. <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithAuth/Android" target="_blank">здесь</a>.

Для изучения этого учебника необходимы Eclipse и Android 4.2 или более поздней версии. 

<h2><a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности</h2>

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

3. В Eclipse откройте проект, созданный при прохождении учебника [Начало работы с мобильными службами]. 

4. Откройте меню **Выполнить** и щелкните **Выполнить**, чтобы запустить приложение; проверьте, происходит ли активация необработанного исключения с кодом состояния 401 (Не авторизовано) после запуска приложения. 

	 Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a>Добавление проверки подлинности к приложению</h2>

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Кэширование токенов проверки подлинности на клиенте

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Обновление кэша токенов

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 



## <a name="next-steps"></a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение идентификатора пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройки мобильных служб]: #register
[Предоставление разрешений на таблицу пользователям, прошедшим проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Сохранение токенов проверки подлинности на клиенте]: #cache-tokens
[Обновление токенов с истекшим сроком действия]: #refresh-tokens
[Следующие шаги]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android

[Портал управления Azure]: https://manage.windowsazure.com/
