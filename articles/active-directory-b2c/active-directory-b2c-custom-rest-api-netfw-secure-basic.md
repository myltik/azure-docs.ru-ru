---
title: Azure Active Directory B2C. Защита служб RESTful с использованием обычной проверки подлинности HTTP
description: Сведения о защите пользовательского обмена утверждениями REST API в системе Azure AD B2C с помощью обычной проверки подлинности HTTP
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.openlocfilehash: 749157d16c1c394b173545dddb8751d58fdcfd56
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Защита служб RESTful с использованием обычной проверки подлинности HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В [связанной статье об Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md) вы создаете службу RESTful (веб-API), которая интегрируется с Azure Active Directory B2C (Azure AD B2C) в пути взаимодействия пользователя без использования проверки подлинности. 

В этой статье вы добавите обычную проверку подлинности HTTP к вашей службе RESTful, чтобы дать возможность проверенным пользователям, включая B2C, получать доступ к вашему API. При использовании обычной проверки подлинности HTTP учетные данные пользователя (идентификатор и секрет приложения) указываются в настраиваемой политике. 

Дополнительные сведения см. в статье [Basic authentication in ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication) (Обычная проверка подлинности в веб-API ASP.NET).

## <a name="prerequisites"></a>предварительным требованиям
Выполните действия из статьи: [Интеграция обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных](active-directory-b2c-custom-rest-api-netfw.md).

## <a name="step-1-add-authentication-support"></a>Шаг 1. Добавление поддержки аутентификации

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Шаг 1.1. Добавление параметров приложения в файл проекта web.config
1. Откройте ранее созданный проект Visual Studio. 

2. Добавьте следующие параметры приложения в элемент `appSettings` в файле web.config:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Создайте пароль, а затем задайте значение `WebApp:ClientSecret`.

    Для генерации сложного пароля запустите следующий код PowerShell. Вы можете использовать любое значение пароля.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Шаг 1.2. Установка библиотек OWIN
Сначала добавьте в проект пакеты NuGet для ПО промежуточного слоя OWIN с помощью консоли диспетчера пакетов Visual Studio.

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Шаг 1.3. Настройка класса ПО промежуточного слоя для аутентификации
Добавьте класс `ClientAuthMiddleware.cs` в папку *App_Start*. Для этого выполните следующие действия:

1. Щелкните правой кнопкой мыши папку *App_Start*, выберите **Добавить**, а затем — **Класс**.

   ![Добавление класса ClientAuthMiddleware.cs в папку App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. В поле **Имя** введите **ClientAuthMiddleware.cs**.

   ![Создание нового класса C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Откройте файл *App_Start\ClientAuthMiddleware.cs* и замените содержимое этого файла следующим кодом:

    ```csharp
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Шаг 1.4. Добавление класса запуска OWIN
Добавьте класс запуска OWIN с именем `Startup.cs` в API. Для этого выполните следующие действия:
1. Щелкните проект правой кнопкой мыши и выберите **Добавить** > **Новый элемент**, после чего найдите **OWIN**.

   ![Добавление класса запуска OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Откройте файл *Startup.cs* и замените содержимое этого файла следующим кодом:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                    app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Шаг 1.5. Защита класса API для идентификации
Откройте файл Controllers\IdentityController.cs и добавьте в класс контроллера тег `[Authorize]`. Тег предоставляет доступ к контроллеру только тем пользователям, которые отвечают требованиям к авторизации.

![Добавление тега [Authorize] в контроллер](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Шаг 2. Публикация в Azure
Чтобы опубликовать проект, в обозревателе решений щелкните правой кнопкой мыши проект **Contoso.AADB2C.API** и выберите **Опубликовать**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Шаг 3. Добавление идентификатора и секрета приложения службы RESTful в Azure AD B2C
Защитив службу RESTful при помощи идентификатора (имя пользователя) и секрета клиента, следует сохранить эти учетные данные в клиенте Azure AD B2C. Пользовательская политика предоставляет учетные данные при вызове служб RESTful. 

### <a name="step-31-add-a-restful-services-client-id"></a>Шаг 3.1. Добавление идентификатора клиента служб RESTful
1. В клиенте Azure AD B2C выберите **B2C Settings** (Параметры B2C)  >  **Identity Experience Framework**.


2. Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.

3. Выберите **Добавить**.

4. В пункте **Параметры** выберите **Manual** (Вручную).

5. Для параметра **Имя** введите значение **B2cRestClientId**.  
    Префикс *B2C_1A_* может быть добавлен автоматически.

6. В поле **Секрет** введите ИД приложения, который вы задали ранее.

7. Для параметра **Key usage** (Использование ключа) выберите **Секрет**.

8. Нажмите кнопку **Создать**.

9. Убедитесь, что вы создали ключ `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-a-restful-services-client-secret"></a>Шаг 3.2. Добавление секрета клиента служб RESTful
1. В клиенте Azure AD B2C выберите **B2C Settings** (Параметры B2C)  >  **Identity Experience Framework**.

2. Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.

3. Выберите **Добавить**.

4. В пункте **Параметры** выберите **Manual** (Вручную).

5. Для параметра **Имя** введите значение **B2cRestClientSecret**.  
    Префикс *B2C_1A_* может быть добавлен автоматически.

6. В поле **Секрет** введите секрет приложения, который вы задали ранее.

7. Для параметра **Key usage** (Использование ключа) выберите **Секрет**.

8. Нажмите кнопку **Создать**.

9. Убедитесь, что вы создали ключ `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Шаг 4. Изменение технического профиля для поддержки обычной проверки подлинности в политике расширения
1. В рабочей папке откройте файл политики расширения (TrustFrameworkExtensions.xml).

2. Найдите узел `<TechnicalProfile>`, содержащий `Id="REST-API-SignUp"`.

3. Найдите элемент `<Metadata>`.

4. Измените значение *AuthenticationType* на *Basic*, как показано ниже:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Сразу после завершения элемента `<Metadata>` добавьте следующий фрагмент XML-кода: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    После добавления фрагмента кода технический профиль должен выглядеть аналогично следующему XML-коду:
    
    ![Добавление XML-элементов для простой аутентификации](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Шаг 5. Отправка политики в клиент

1. На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте **Azure AD B2C**.

2. Выберите **Инфраструктура процедур идентификации**.

3. Откройте **Все политики**.

4. Щелкните **Отправить политику**.

5. Установите флажок **Перезаписать политику, если она существует**.

6. Отправьте файл *TrustFrameworkExtensions.xml* и немного подождите, чтобы удостовериться, что он прошел проверку.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Шаг 6. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1. Откройте **параметры Azure AD B2C** и выберите **Identity Experience Framework**.

    >[!NOTE]
    >Для использования команды Run now (Запустить сейчас) необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

2. Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны, а затем выберите **Run Now** (Запустить сейчас).

3. Проверьте процесс, введя **Test** в поле **Имя**.  
    В верхней части страницы Azure AD B2C отобразится сообщение об ошибке.

    ![Проверка API для идентификации](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. В поле **Имя** введите имя, отличное от Test.  
    Azure AD B2C зарегистрирует нового пользователя и отправит в ваше приложение номер лояльности. Обратите внимание на номер в этом примере:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>Скачивание полного текста файлов политики и кода (необязательно)
* После того как вы ознакомитесь с [пошаговым руководством по началу работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md), рекомендуем создать свой сценарий, используя собственные файлы пользовательской политики. Для справки мы предоставили [образцы файлов политики](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Вы можете загрузить полный код из примера решения Visual Studio [отсюда](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Дополнительная информация
* [Azure Active Directory B2C: Secure your RESTful services using client certificates](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) (Azure Active Directory B2C: защита служб RESTful с помощью клиентских сертификатов)

