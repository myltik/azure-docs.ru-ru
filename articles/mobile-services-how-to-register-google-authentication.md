<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Регистрация для проверки подлинности Google" pageTitle="Регистрация для проверки подлинности Google — мобильные службы" metaKeywords="Регистрация приложения Azure, проверки подлинности Google в Azure, проверка подлинности приложения, проверка подлинности мобильных служб" description="Узнайте, как зарегистрировать ваше приложение для использования Google при проверке подлинности в службах мультимедиа Azure." metaCanonical="" services="" documentationCenter="" title="Регистрация приложений для входа в Google с помощью мобильных служб" authors="" solutions="" manager="" editor="" />

# Регистрация приложений для входа в Google с помощью мобильных служб

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Google для проверки подлинности с помощью мобильных служб Azure.

<div class="dev-callout"><b>Примечание.</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> и войдите с помощью учетной записи Google. Нажмите кнопку **Создать проект...**.

   	![][1]   

2. Нажмите кнопку **Доступ к API** и затем нажмите **Создать идентификатор клиента OAuth 2.0.**.

   	![][2]

3. В поле **Сведения о фирменном стиле** введите **имя продукта** и нажмите кнопку **Далее**.  

   	![][3]

4. В разделе **Параметры кода клиента** выберите **Веб-приложение**, введите URL-адрес мобильной службы в поле **Сайт или имя узла**, нажмите кнопку **Дополнительные параметры**, замените созданный URL-адрес в поле **Авторизованные URI перенаправления** на URL-адрес вашей мобильной службы, дополненный путем _/login/google_, и нажмите кнопку **Создать код клиента**.

   	![][4]

	>[WACOM.NOTE]Для серверной мобильной службы .NET, опубликованной в Azure с помощью Visual Studio, URL-адресом перенаправления является URL-адрес вашей мобильной службы, дополненный путем __Hlk384692799signin-google__Hlk384692799 мобильной службы в качестве службы .NET, например <code>https://todolist.azure-mobile.net/signin-google</code>.

6. В разделе **Код клиента для веб-приложений** запишите значения полей **Код клиента** и **Секрета клиент**. 

   	![][5]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе с вашим приложением.</p>
    </div>

Теперь вы готовы использовать имя входа Google для проверки подлинности в вашем приложении, предоставляя мобильным службам код клиента и секрет клиента приложения.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[API-интерфейсы Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/

[Портал управления Azure]: https://manage.windowsazure.com/

