<properties 
	pageTitle="Регистрация для проверки подлинности Facebook — мобильные службы" 
	description="Сведения об использовании проверки подлинности в Facebook в приложении мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/27/2015" 
	ms.author="glenga"/>

# Регистрация приложений для аутентификации в мобильных службах с помощью Facebook

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Facebook для аутентификации в мобильных службах Azure.

>[AZURE.NOTE]Это учебник о [мобильных службах Azure] — решении, которое помогает создавать масштабируемые мобильные приложения для любых платформ. С мобильными службами легко синхронизировать данные, аутентифицировать пользователей и отправлять push-уведомления. Эта страница дополняет учебник <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Приступая к работе с проверкой подлинности</a>, в котором показано, как настроить вход пользователей в приложение. Если это ваш первый опыт работы с мобильными службами, сначала ознакомьтесь с учебником <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Приступая к работе с мобильными службами</a>.
	
Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Facebook с проверенным электронным адресом и номером мобильного телефона. Чтобы создать новую учетную запись Facebook, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">разработчиков Facebook</a> и войдите с помощью учетной записи Facebook.

2. (Необязательно) Если вы еще не зарегистрированы, нажмите кнопку **My Apps** (Мои приложения), затем **Register as a Developer** (Зарегистрироваться как разработчик), примите политику и выполните действия для регистрации.

3. Щелкните последовательно **My Apps** (Мои приложения) > **Add a New App** (Добавить новое приложение) > **Advanced setup** (Расширенная настройка).

4. Введите уникальное **отображаемое имя** для своего приложения, в разделе **Категория** выберите **Приложения для страниц**, после чего нажмите кнопку **Create App ID** (Создать идентификатор приложения) и завершите процедуру безопасности.

	В результате будет создан идентификатор приложения Facebook.

5. Щелкните **Settings** (Параметры), введите имя домена вашей мобильной службы в поле **App Domains** (Домены приложения), заполните, если нужно, поле **Contact Email** (Контактный адрес электронной почты), щелкните **Add Platform** (Добавить платформу) и выберите **веб-сайт**.

   	![][3]

6. Введите URL-адрес вашей мобильной службы в поле **URL-адрес сайта** и нажмите кнопку **Сохранить изменения**.

7. Нажмите кнопку **Показывать**, введите пароль при запросе, а затем запишите значения **ИД приложения** и **Секрет приложения**.

   	![][5] &nbsp;
	
    >[AZURE.IMPORTANT]Секрет приложения — это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе с вашим приложением. &nbsp;

8. Щелкните вкладку **Дополнительно**, введите URL-адрес своей мобильной службы с добавлением пути _/login/facebook_ в разделе **Допустимые универсальные коды ресурса (URI) перенаправления OAuth**, а затем щелкните **Сохранить изменения**. &nbsp;

     >[AZURE.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-facebook_ мобильной службы в качестве службы .NET, например `https://todolist.azure-mobile.net/signin-facebook`.
       

9. Чтобы сделать свое приложение общедоступным, щелкните **Status & Review** (Состояние и просмотр) > **Yes** (Да).

	Учетная запись Facebook, используемая для регистрации нового приложения, является администратором приложения и имеет доступ к нему с правами администратора. Когда вы выполните эти действия, приложение станет общедоступным и благодаря этому сможет проводить проверку подлинности, используя другие учетные записи Facebook.


Теперь вы готовы использовать имя входа Facebook для проверки подлинности в вашем приложении, предоставляя мобильным службам значения ИД приложения и секретного кода приложения.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
[мобильных службах Azure]: http://azure.microsoft.com/services/mobile-services/
 

<!---HONumber=July15_HO2-->