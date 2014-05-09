<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Регистрация для проверки подлинности в Facebook" pageTitle="Регистрация для проверки подлинности в Facebook — мобильные службы" metaKeywords="Windows Azure Facebook, Azure Facebook, проверки подлинности, Azure, мобильные службы" description="Использование проверки подлинности в Facebook в ваших приложениях для мобильных служб Windows Azure." metaCanonical="" services="" documentationCenter="" title="Регистрация приложений для проверки подлинности в Facebook с помощью мобильных служб" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# Регистрация приложений для проверки подлинности в Facebook с помощью мобильных служб

В этом разделе показано, как зарегистрировать приложения, чтобы иметь возможность использовать Facebook для проверки подлинности с помощью мобильных служб Windows Azure. 

<div class="dev-callout"><b>Примечание.</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Facebook с проверенным адресом электронной почты и номером мобильного телефона. Чтобы создать новую учетную запись Facebook, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.</p>
</div> 

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Разработчики Facebook</a> и войдите с помощью ваших данных учетной записи Facebook.

2. (Необязательно) Если вы еще не зарегистрированы, нажмите кнопку **Приложения**, затем **Зарегистрироваться в качестве разработчика**, примите политику и выполните действия по регистрации. 

   	![][0]

3. Нажмите кнопку **Приложения**, а затем щелкните **Создать новое приложение**

   	![][1]

4. Выберите уникальное имя для приложения, выберите **Приложения для страниц**, щелкните **Создать приложение** и выполните запрос.

   	![][2]

	При этом приложение зарегистрируется в Facebook 

5. Щелкните **Параметры**, введите имя домена вашей мобильной службы в поле **Домены приложения**, щелкните **Добавить платформу** и выберите **Веб-узел**.

   	![][3]

6. Введите URL-адрес вашей мобильной службы в поле **URL-адрес сайта** и нажмите кнопку **Сохранить изменения**.

	![][4]

7. Нажмите кнопку **Показывать**, введите пароль при запросе, а затем запишите значения **ИД приложения** и **Секрет приложения**. 

   	![][5]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет приложения — это важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе с вашим приложением.</p>
    </div>

Теперь вы готовы использовать имя входа Facebook для проверки подлинности в вашем приложении, предоставляя мобильным службам значения ИД приложения и секретного кода приложения.  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Разработчики Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Портал управления Windows Azure]: https://manage.windowsazure.com/

