

Чтобы иметь возможность аутентифицировать пользователей, нужно зарегистрировать приложение в поставщике удостоверений. Затем необходимо зарегистрировать секрет клиента, созданный поставщиком, в службе приложений.

1. Войдите на [портал управления Azure], последовательно щелкните **Просмотр** и **Группы ресурсов**, а затем выберите группу своего мобильного приложения.

2. Выберите шлюз и запишите **URL-адрес** (указан в разделе **Свойства**). От вас может потребоваться предоставить это значение поставщику удостоверений при регистрации приложения.

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. Выберите в приведенном ниже списке поддерживаемого поставщика удостоверений и выполните указания по настройке своего приложения для этого поставщика.

 - <a href="/ru-ru/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/ru-ru/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Имя для входа Facebook</a>
 - <a href="/ru-ru/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Имя для входа Google</a>
 - <a href="/ru-ru/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Учетная запись Майкрософт</a>
 - <a href="/ru-ru/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Имя для входа Twitter</a>

	После этого ваше приложение сможет работать с выбранным поставщиком проверки подлинности.

<!-- URLs. -->
[Портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=49-->