## <a name="register-mobile-service-aad"></a>Регистрация мобильной службы в Azure Active Directory


В этом разделе вы узнаете, как зарегистрировать мобильную службу с помощью Azure Active Directory и настроить разрешения олицетворения единого входа.

1. Зарегистрируйте приложение в Azure Active Directory, следуя указаниям в разделе [Регистрация в Azure Active Directory].

2. На [портале управления Azure] вернитесь к расширению Azure Active Directory и щелкните активный каталог.

3. Щелкните вкладку **Приложения**, а затем выберите свое приложение.

4. Щелкните **Управление манифестом**. Затем щелкните **Загрузить манифест** и сохраните манифест приложения в локальный каталог.

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. Откройте файл манифеста приложения с помощью Visual Studio. В верхней части файла найдите строку разрешений приложения, которая выглядит аналогичным образом:

        "oauth2Permissions": [],

    Замените строку следующими разрешениями приложения и сохраните файл.

        "oauth2Permissions": [
            {
                "adminConsentDescription": "Allow the application access to the mobile service",
                "adminConsentDisplayName": "Have full access to the mobile service",
                "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "isEnabled": true,
                "origin": "Application",
                "type": "User",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service",
                "value": "user_impersonation"
            }
        ],

6. На портале управления Azure снова щелкните **Управление манифестом** для приложения и щелкните **Отправить манифест**.  Откройте обновленную папку манифеста приложения и отправьте манифест.

<!-- URLs. -->
[Регистрация в Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[портале управления Azure]: https://manage.windowsazure.com/
\<!--HONumber=42-->
