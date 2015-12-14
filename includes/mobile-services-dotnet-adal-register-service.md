## <a name="register-mobile-service-aad"></a>Регистрация мобильной службы в Azure Active Directory


В этом разделе вы узнаете, как зарегистрировать мобильную службу с помощью Azure Active Directory и настроить разрешения олицетворения единого входа.

1. Зарегистрируйте приложение с помощью Azure Active Directory, следуя инструкциям в разделе [Регистрация с помощью Azure Active Directory].

2. На [классическом портале Azure](https://manage.windowsazure.com/) вернитесь к расширению Azure Active Directory и щелкните активный каталог

3. Щелкните вкладку **Приложения**, а затем выберите приложение.

4. Нажмите кнопку **Управление манифестом**. Затем нажмите кнопку **Скачать манифест** и сохраните манифест приложения в локальный каталог.

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

6. Снова щелкните на [классическом портале Azure](https://manage.windowsazure.com/) кнопку **Управление манифестом** для приложения и нажмите **Отправить манифест**. Откройте обновленную папку манифеста приложения и отправьте манифест.

<!-- URLs. -->
[Регистрация с помощью Azure Active Directory]: ../articles/mobile-services/mobile-services-how-to-register-active-directory-authentication.md

<!---HONumber=AcomDC_1203_2015-->