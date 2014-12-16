## <a name="register-mobile-service-aad"></a>Регистрация мобильной службы в Azure Active Directory


В этом разделе вы узнаете, как зарегистрировать мобильную службу с помощью Azure Active Directory и настроить разрешения олицетворения единого входа.

1. Зарегистрируйте приложение в Azure Active Directory, следуя инструкциям в разделе [Регистрация в Azure Active Directory].

2. На [портале управления Azure] вернитесь к расширению Azure Active Directory и щелкните активный каталог

3. Откройте вкладку **Приложения**, а затем выберите приложение.

4. Щелкните **Управление манифестом**. Затем нажмите кнопку **Загрузить манифест** и сохраните манифест приложения в локальный каталог.

   ![](./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png)

5. Откройте файл манифеста приложения в среде Visual Studio. В верхней части файла найдите строку разрешений приложения, которая выглядит примерно так:

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

6. Снова нажмите на портале управления Azure кнопку **Управление манифестом** для приложения и нажмите **Отправить манифест**.  Откройте обновленную папку манифеста приложения и отправьте манифест.

<!-- URLs. -->
[Регистрация в службе Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Портал управления Azure]: https://manage.windowsazure.com/
