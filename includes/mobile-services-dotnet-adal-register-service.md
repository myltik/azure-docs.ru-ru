## <a name="register-mobile-service-aad"></a>Регистрация мобильной службы с помощью Azure Active Directory

В этом разделе вы узнаете, как зарегистрировать мобильную службу с помощью Azure Active Directory и настроить разрешения олицетворения единого входа.

1.  Зарегистрируйте приложение с помощью Azure Active Directory, следуя инструкциям в разделе [Регистрация с помощью Azure Active Directory][].

2.  На [портале управления Azure][] вернитесь к расширению Azure Active Directory и щелкните активный каталог

3.  Щелкните вкладку **Приложения**, а затем выберите приложение.

4.  Нажмите кнопку **Управление манифестом**. Затем нажмите кнопку **Скачать манифест** и сохраните манифест приложения в локальный каталог.

![][ ]

1.  Откройте файл манифеста приложения с помощью Visual Studio. В верхней части файла найдите строку разрешений приложения, которая выглядит аналогичным образом:

        "appPermissions": [],

    Замените строку следующими разрешениями приложения и сохраните файл.

        "appPermissions": [
            {
                "claimValue": "user_impersonation",
                "description": "Allow the application access to the mobile service",
                "directAccessGrantTypes": [],
                "displayName": "Have full access to the mobile service",
                "impersonationAccessGrantTypes": [
                    {
                        "impersonated": "User",
                        "impersonator": "Application"
                    }
                ],
                "isDisabled": false,
                "origin": "Application",
                "permissionId": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "resourceScopeType": "Personal",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service"
            }
        ],

2.  Снова щелкните на портале управления Azure кнопку **Управление манифестом** для приложения и нажмите **Отправить манифест**. Откройте обновленную папку манифеста приложения и отправьте манифест.

<!-- URLs. -->

  [Регистрация с помощью Azure Active Directory]: /ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [портале управления Azure]: https://manage.windowsazure.com/
  [ ]: ./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png
