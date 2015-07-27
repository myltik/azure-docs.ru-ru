<properties 
	pageTitle="Приступая к работе с проверкой подлинности (JavaScript) | Центр мобильных разработок" 
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения JavaScript магазина Windows с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>

# Добавление проверки подлинности к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Предоставление разрешений на таблицу только пользователям, прошедшим проверку подлинности]
3. [Добавление проверки подлинности в приложение]
4. [Хранение маркеров проверки подлинности в клиенте]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

>[AZURE.NOTE]В этом учебнике показан поток проверки подлинности, управляемый мобильными службами с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. Информацию о том, как вместо этого использовать Live Connect для аутентификации, управляемой клиентом, и предоставления единого входа в приложении для Windows Phone, см. в разделе [Единый вход для приложений Windows Phone с использованием Live Connect]. При использовании управляемой клиентом проверки подлинности приложение имеет доступ к дополнительным данным пользователя, сохраняемым поставщиком удостоверений. Чтобы получить те же данные о пользователе в мобильной службе, вызовите в серверных сценариях функцию **user.getIdentities()**. Дополнительную информацию см. в [этой записи](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

<ol start="5">
<li><p>(Необязательно) Выполните шаги, указанные в пункте <a href="/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Регистрация пакета приложения магазина Windows для проверки подлинности Microsoft</a>.</p>

    
	<p>Обратите внимание, что этот шаг не является обязательным, так как он применяется только к поставщику входа учетной записи Майкрософт. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Выполните этот шаг, если вы планируете использовать поставщик удостоверений учетной записи Майкрософт.</p>
</li>
</ol>
Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

##<a name="permissions"></a> Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>Откройте проект, созданный после завершения обучения, в Visual Studio 2012 Express для Windows 8 <a href="/develop/mobile/tutorials/get-started/">Приступая к работе с мобильными службами</a>.</p></li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a> Добавление проверки подлинности в приложение

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

##<a name="tokens"></a>Хранение маркеров проверки подлинности в клиенте

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы][Authorize users with scripts], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.


<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Предоставление разрешений на таблицу только пользователям, прошедшим проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Хранение маркеров проверки подлинности в клиенте]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений Windows Phone с использованием Live Connect]: mobile-services-windows-store-javascript-single-sign-on.md
[Приступая к работе с мобильными службами]: ../mobile-services-windows-store-get-started.md
[Get started with data]: mobile-services-windows-store-javascript-get-started-data.md
[Get started with authentication]: mobile-services-windows-store-javascript-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-javascript-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Register your Windows Store app package for Microsoft authentication]: /develop/mobile/how-to-guides/register-windows-store-app-package
 

<!---HONumber=July15_HO3-->