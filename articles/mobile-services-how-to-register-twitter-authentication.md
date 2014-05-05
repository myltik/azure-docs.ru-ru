<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Регистрация для проверки подлинности Twitter" pageTitle="Регистрация для проверки подлинности Twitter — мобильные службы" metaKeywords="Регистрация приложения Azure, проверки подлинности Twitter в Azure, проверка подлинности приложения, проверка подлинности мобильных служб, Twitter и мобильные службы" description="Узнайте, как использовать проверку подлинности Twitter с приложением служб мультимедиа Azure." metaCanonical="" services="" documentationCenter="" title="Регистрация приложений для входа в Twitter с помощью мобильных служб" authors="" solutions="" manager="" editor="" />

#Регистрация приложений для входа в Twitter с помощью мобильных служб

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Twitter для проверки подлинности с помощью мобильных служб Azure.

<div class="dev-callout"><b>Примечание.</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Twitter с проверенным адресом электронной почты. Чтобы создать учетную запись Twitter, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.</p>
</div> 

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Разработчики Twitter</a> и войдите с помощью учетной записи Twitter. Нажмите кнопку **Создать приложение**.

   	![][1]

2. Введите значения в поля **Имя**, **Описание**, и **Веб-сайт** для вашего приложения и введите URL-адрес мобильной службы в поле **URL-адрес обратного вызова**.

   	![][2]

    
	>[WACOM.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем _signin-google_ мобильной службы в качестве службы .NET, например <code>https://todolist.azure-mobile.net/signin-twitter</code>. <br />Значение поля <strong>Веб-сайт</strong> является обязательным, но не используется.

3.  В нижней части страницы прочитайте и примите условия соглашения, введите текст проверки CAPTCHA и нажмите кнопку **Создать приложение Twitter**. 

   	![][3]

   	После этого приложение будет зарегистрировано и появятся сведения о приложении.

6. Запомните значения полей **Ключ клиента** и **Секрет клиента**. 

   	![][4]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе с вашим приложением.</p>
    </div>

7. Откройте вкладку **Параметры**, прокрутите экран вниз и установите флажок **Разрешить этому приложению вход с помощью Twitter**. Затем нажмите кнопку **Обновить параметры приложения Twitter**.

	![][5]

Теперь вы готовы использовать имя входа Twitter для проверки подлинности в вашем приложении, предоставляя мобильным службам ключ клиента и секрет клиента.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Разработчики Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/

[Портал управления Azure]: https://manage.windowsazure.com/

