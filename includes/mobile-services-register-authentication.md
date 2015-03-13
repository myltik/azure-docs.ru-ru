

Чтобы иметь возможность аутентифицировать пользователей, нужно зарегистрировать приложение в поставщике удостоверений. Затем необходимо зарегистрировать секрет клиента, созданный поставщиком, в мобильных службах.

1. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2. Щелкните вкладку **Панель мониторинга** и запишите значение **URL-адрес мобильной службы**.

   	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    От вас может потребоваться предоставить это значение поставщику удостоверений при регистрации приложения.

3. Выберите поддерживаемого поставщика удостоверений из приведенного ниже списка и выполните действия для регистрации приложения у этого поставщика:

 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Учетная запись Майкрософт</a>
 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Имя для входа Facebook</a>
 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Имя для входа Twitter</a>
 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Имя для входа Google</a>
 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Не забудьте записать удостоверение клиента и значения секрета, созданные поставщиком.

    > [AZURE.IMPORTANT] Секрет, созданный поставщиком, относится к важным учетным данным безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

4. На портале управления нажмите вкладку **Удостоверение**, введите идентификатор приложения и общие значения секрета, полученные у поставщика удостоверений, а затем щелкните **Сохранить**.

   	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

	Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

<!-- URLs. -->
[Портал управления Azure]: https://manage.windowsazure.com/
\<!--HONumber=42-->
