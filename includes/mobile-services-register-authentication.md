Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать приложение у поставщика удостоверений. Затем необходимо зарегистрировать секрет клиента, созданный поставщиком, в мобильных службах.

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните по пункту **Мобильные службы**, а затем щелкните по своей мобильной службе.

    ![][0]

2.  Щелкните вкладку **Панель мониторинга** и запишите значение **URL-адрес мобильной службы**.

    ![][1]

    От вас может потребоваться предоставить это значение поставщику удостоверений при регистрации приложения.

3.  Выберите поддерживаемого поставщика удостоверений из приведенного ниже списка и выполните действия для регистрации приложения у этого поставщика:

-   [Учетная запись Майкрософт][Учетная запись Майкрософт]
-   [Вход в Facebook][Вход в Facebook]
-   [Вход в Twitter][Вход в Twitter]
-   [Вход в Google][Вход в Google]
-   [Azure Active Directory][Azure Active Directory]

    Не забудьте записать удостоверение клиента и значения секрета, созданные поставщиком.

    <div class="dev-callout">

    **Примечание о безопасности**
    Секрет, созданный поставщиком, входит в важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

    </div>

1.  На портале управления нажмите вкладку **Удостоверение**, введите идентификатор приложения и общие значения секрета, полученные у поставщика удостоверений, а затем щелкните **Сохранить**.

    ![][2]

    Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.



  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-register-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-register-authentication/mobile-service-uri.png
  [Учетная запись Майкрософт]: /ru-ru/documentation/articles/mobile-services-how-to-register-microsoft-authentication/
  [Вход в Facebook]: /ru-ru/documentation/articles/mobile-services-how-to-register-facebook-authentication/
  [Вход в Twitter]: /ru-ru/documentation/articles/mobile-services-how-to-register-twitter-authentication/
  [Вход в Google]: /ru-ru/documentation/articles/mobile-services-how-to-register-google-authentication/
  [Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [2]: ./media/mobile-services-register-authentication/mobile-identity-tab.png
