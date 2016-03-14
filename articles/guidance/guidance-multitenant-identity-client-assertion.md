<properties
   pageTitle="Получение маркеров доступа из Azure AD с помощью утверждений клиентов | Microsoft Azure"
   description="Сведения об использовании утверждений клиентов для получения маркеров доступа из Azure AD."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Получение маркеров доступа из Azure AD с помощью утверждений клиентов

Этот материал входит в [цикл статей]. К статьям прилагается полный [пример приложения].

## Общие сведения

При использовании потока кода авторизации или гибридного потока в OpenID Connect клиент обменивается кодом авторизации для маркера доступа. На этом этапе клиент должен пройти проверку подлинности на сервере.

![Секрет клиента](media/guidance-multitenant-identity/client-secret.png)

Один из способов проверки подлинности клиента заключается в использовании секрета клиента. Именно так по умолчанию настроено приложение [Tailspin Surveys][Surveys].

Ниже приведен пример запроса клиента на маркер доступа от поставщика удостоверений. Обратите внимание на параметр `client_secret`.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

Секрет представляет собой просто строку, поэтому необходимо защитить его от раскрытия. Секрет клиента рекомендуется хранить вне системы управления версиями. При развертывании в Azure сохраните секрет в [параметре приложения][configure-web-app].

Однако просматривать параметры приложения может любой пользователь, имеющий доступ к подписке Azure. Кроме того, всегда существует соблазн проверить секреты в системе управления версиями (например в сценариях развертывания), отправить их по электронной почте и т. д.

Для обеспечения дополнительной безопасности вместо секрета клиента можно использовать _утверждение клиента_. При наличии утверждения клиент использует сертификат X.509 для доказательства получения запроса на маркер от клиента. Сертификат клиента установлен на веб-сервере. Как правило, будет проще ограничить доступ к сертификату, чем предотвратить непреднамеренное раскрытие секрета клиента.

Ниже приведен запрос на маркер с помощью утверждения клиента.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Обратите внимание, что параметр `client_secret` больше не используется. Параметр `client_assertion` содержит маркер JWT, который был подписан с помощью сертификата клиента. Параметр `client_assertion_type` определяет тип утверждения: в этом случае — маркер JWT. Сервер проверяет маркер JWT. Если маркер JWT является недопустимым, запрос на маркер возвращает ошибку.

> [AZURE.NOTE] Сертификаты X.509 не являются единственным видом утверждения клиента. Мы рассматриваем их, поскольку они поддерживаются в Azure AD.

## Использование утверждений клиентов в приложении Surveys

В этом разделе показано, как настроить приложение Tailspin Surveys для использования утверждения клиента. В ходе процедуры вы создадите самозаверяющий сертификат, который подходит для разработки, но не для использования в рабочей среде.

1. Запустите сценарий PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault], как показано ниже.

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Для параметра `Subject` введите любое имя, например "surveysapp". Сценарий создаст самозаверяющий сертификат и сохранит его в хранилище сертификатов "Текущий пользователь/Личные".

2. Результатом этого сценария является фрагмент JSON. Добавьте его в манифест веб-приложения, как показано ниже:

    1. Войдите на [портал управления Azure][azure-management-portal] и перейдите в свой каталог Azure AD.

    2. Щелкните **Приложения**.

    3. Выберите приложение Surveys.

    4.	Щелкните **Управление манифестом** и выберите **Скачать манифест**.

    5.	Откройте JSON-файл манифеста в текстовом редакторе. Вставьте выходные данные сценария в свойство `keyCredentials`. Это должно выглядеть следующим образом:

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.	Сохраните изменения в JSON-файле.

    7.	Вернитесь на портал. Щелкните **Управление манифестом** > **Отправить манифест** и отправьте JSON-файл.

3. Выполните следующую команду, чтобы получить отпечаток сертификата:

    ```
    certutil -store -user my [subject]
    ```

    где `[subject]` — это значение, указанное для Subject в сценарии PowerShell. Отпечаток указан в разделе "Cert Hash(sha1)". Удалите пробелы между шестнадцатеричными значениями.

4. Обновите секреты приложения. В обозревателе решений щелкните правой кнопкой мыши проект Tailspin.Surveys.Web и выберите **Управление секретами пользователей**. Добавьте запись для "Asymmetric" в разделе "AzureAd", как показано ниже.

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    `ValidationRequired` необходимо задать значение "false", поскольку сертификат не был подписан корневым центром сертификации. В рабочей среде используйте сертификат, подписанный центром сертификации, и задайте `ValidationRequired` значение "true".

    Также удалите запись для `ClientSecret`, так как она не нужна для утверждения клиента.

5. В Startup.cs найдите код, который регистрирует `ICredentialService`. Раскомментируйте строку, которая использует `CertificateCredentialService`, и закомментируйте строку, которая использует `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

Во время выполнения веб-приложение считывает сертификат из хранилища сертификатов. Сертификат должен быть установлен на одном компьютере с веб-приложением.

## Дополнительные ресурсы

- [Использование сертификатов в приложениях веб-сайтов Azure][using-certs-in-websites]
- [RFC 7521][RFC7521]. Определяет общий механизм для отправки утверждения клиента.
- [RFC 7523][RFC7523]. Определяет способ использования маркеров JWT для утверждения клиента.


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[цикл статей]: guidance-multitenant-identity.md
[пример приложения]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->