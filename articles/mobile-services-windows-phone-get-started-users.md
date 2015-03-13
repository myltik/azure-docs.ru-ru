<properties 
	pageTitle="Знакомство с механизмом проверки подлинности (Windows Phone) | Центр разработчиков для мобильных устройств" 
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения Windows Phone с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." 
	services="mobile-services" 
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
	ms.date="09/23/2014" 
	ms.author="glenga"/>

# Добавление проверки подлинности в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:50</span></div>
</div>  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

>[AZURE.NOTE]В этом учебнике показан поток проверки подлинности, управляемый мобильными службами с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. Сведения о том, как вместо этого использовать Live Connect для управляемой клиентом проверки подлинности и предоставления единого входа в приложении Windows Phone, см. в разделе [Единый вход для приложений Windows Phone с использованием Live Connect]. При использовании управляемой клиентом проверки подлинности приложение имеет доступ к дополнительным данным пользователя, сохраняемым поставщиком удостоверений. Эти же данные можно получить в мобильной службе, вызвав в серверных скриптах функцию **user.getIdentities()**. Дополнительные сведения можно найти в [этой записи блога](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб


[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 


##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. В Visual Studio 2012 Express для Windows Phone откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами]. 

4. Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). 
   
   	Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Хранение маркеров авторизации в клиенте

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей с помощью скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-per
	ms.png

<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts
[Портал управления Azure]: https://manage.windowsazure.com/
[Единый вход для приложений Windows Phone с использованием Live Connect]: /ru-ru/documentation/articles/mobile-services-windows-phone-single-sign-on


<!--HONumber=42-->
