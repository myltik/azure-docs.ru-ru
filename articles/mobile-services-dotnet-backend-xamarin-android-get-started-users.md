<properties urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Приступая к работе с проверкой подлинности в мобильных службах для приложений Android на Xamarin. Мобильные службы Azure" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Приступая к работе с проверкой подлинности в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять аутентификацию учетных данных пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами]. 

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>В Visual Studio или Xamarin Studio запустите проект клиента на устройстве или симуляторе. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный доступ).</p>
   
   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности к приложению

1. Добавьте в класс **TodoActivity** следующее свойство:

			private MobileServiceUser user;

2. Добавьте в класс **TodoActivity** следующий метод: 

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в Facebook. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в метод <strong>LoginAsync</strong> выше, на одно из следующих: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> или <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. В методе **OnCreate**  добавьте следующую строку после кода, который создает объект `MobileServiceClient`.

		// Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Этот вызов запускает процесс проверки подлинности и асинхронно ждет его.


4. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.


<!-- ## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб с помощью этих служб][Проверка подлинности пользователей на основе скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. 
 -->
 
<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript и HTML]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Портал управления Azure]: https://manage.windowsazure.com/
