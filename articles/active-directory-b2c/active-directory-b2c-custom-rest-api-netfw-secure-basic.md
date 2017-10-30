---
title: "Azure Active Directory B2C. Защита служб RESTful с помощью обычной HTTP-аутентификации"
description: "Пример действий по защите пользовательского обмена утверждениями REST API в системе Azure AD B2C с помощью обычной HTTP-аутентификации"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C. Защита служб RESTful с помощью обычной HTTP-аутентификации
В другой [связанной статье](active-directory-b2c-custom-rest-api-netfw.md) мы создали службу RESTful (веб-API), которую можно интегрировать в разные пути пользователя Azure AD B2C без поддержки аутентификации. В этой статье мы покажем, как добавить к службе RESTful обычную HTTP-аутентификацию, чтобы доступ к API могли получать только проверенные пользователи, в том числе B2C. При использовании обычной HTTP-аутентификации учетные данные пользователя (идентификатор и секрет приложения) указываются как обязательные атрибуты в настраиваемой политике. 

> [!NOTE]
>
>Сведения об использовании обычной проверки подлинности в веб-API ASP.NET есть в [этой статье](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Предварительные требования
Выполните действия из предыдущей статьи: [Azure Active Directory B2C: Integrate REST API claims exchanges in your Azure AD B2C user journey as validation on user input](active-directory-b2c-custom-rest-api-netfw.md) (Azure Active Directory B2C. Интеграция обмена утверждениями REST API в пути взаимодействия пользователя Azure AD B2C как проверка пользовательского ввода).

## <a name="step-1-add-authentication-support"></a>Шаг 1. Добавление поддержки аутентификации

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>Шаг 1.1. Добавление параметров приложения в файл web.config проекта
1. Откройте ранее созданный проект Visual Studio. 
2. Добавьте следующие параметры приложения в элемент `appSettings` в файле web.config:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Создайте пароль и введите его в качестве значения `WebApp:ClientSecret`.

    Чтобы создать сложный пароль, можно применить указанный ниже код PowerShell. Но вы можете использовать любое значение пароля.

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

### <a name="step-13-add-authentication-middleware-class"></a>Шаг 1.3. Настройка класса ПО промежуточного слоя для аутентификации
Добавьте класс `ClientAuthMiddleware.cs` в папку `App_Start`. Правой кнопкой мыши щелкните папку `App_Start`, выберите действие **Добавить** и элемент **Класс**

![Добавление класса ClientAuthMiddleware.cs в папку App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

Введите имя класса `ClientAuthMiddleware.cs`.

![Создание нового класса C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

Откройте файл `App_Start\ClientAuthMiddleware.cs` и замените содержимое этого файла следующим кодом:


```C#

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
Добавьте класс запуска OWIN в API с именем `Startup.cs`. Щелкните проект правой кнопкой мыши и выберите **Добавить** и **Новый элемент**, после чего найдите OWIN.

![Добавление класса запуска OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

Откройте файл `Startup.cs` и замените содержимое этого файла следующим кодом:

```C#
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

### <a name="step-15-protect-identity-api-class"></a>Шаг 1.5. Защита класса API для идентификации
Откройте файл Controllers\IdentityController.cs и добавьте в класс контроллера тег `[Authorize]`. Тег `[Authorize]` предоставляет доступ к контроллеру только тем пользователям, которые отвечают требованиям к авторизации.

![Добавление тега [Authorize] в контроллер](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Шаг 2. Публикация в Azure
Чтобы опубликовать проект, в **обозревателе решений** щелкните правой кнопкой мыши проект **Contoso.AADB2C.API** и выберите **Опубликовать**.

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>Шаг 3. Добавление идентификатора и секрета приложения службы RESTful в Azure AD B2C
После включения защиты службы RESTful по идентификатору клиента (имя пользователя) и секрету клиента вам нужно сохранить эти учетные данные в клиенте Azure AD B2C. Пользовательская политика будет предоставлять учетные данные при вызове служб RESTful.  

### <a name="step-31-add-restful-services-client-id"></a>Шаг 3.1. Добавление идентификатора клиента служб RESTful
1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Вручную**.
5.  Для параметра **Имя** используйте значение `B2cRestClientId`.  
    Префикс `B2C_1A_` может быть добавлен автоматически.
6.  В поле **Секрет** введите идентификатор приложения, который вы указали ранее.
7.  Для параметра **Использование ключа** задайте значение **Секрет**.
8.  Нажмите кнопку **Создать**
9.  Убедитесь, что вы создали ключ `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-restful-services-client-secret"></a>Шаг 3.2. Добавление секрета клиента служб RESTful
1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Вручную**.
5.  Для параметра **Имя** используйте значение `B2cRestClientSecret`.  
    Префикс `B2C_1A_` может быть добавлен автоматически.
6.  В поле **Секрет** введите секрет приложения, который вы указали ранее.
7.  Для параметра **Использование ключа** задайте значение **Секрет**.
8.  Нажмите кнопку **Создать**
9.  Убедитесь, что вы создали ключ `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>Шаг 4. Изменение `TechnicalProfile` для поддержки простой аутентификации в политике расширения
1.  Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога.
2.  Найдите узел `<TechnicalProfile>`, который включает `Id="REST-API-SignUp"`.
3.  Найдите элемент `<Metadata>`.
4.  Присвойте `AuthenticationType` значение `Basic`.
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  Добавьте следующий XML-фрагмент сразу после завершения элемента `<Metadata>`:  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
Когда вы добавите XML-фрагмент, узел `TechnicalProfile` должен выглядеть так:

![Добавление XML-элементов для простой аутентификации](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Шаг 5. Отправка политики в клиент

1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и щелкните **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Щелкните **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Шаг 6. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.

    >[!NOTE]
    >
    >    Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. 
    >    Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  Попробуйте ввести Test в поле **Имя**. В верхней части страницы B2C отобразится сообщение об ошибке.

    ![Проверка API для идентификации](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  Попробуйте ввести в поле **Имя** другое имя (не test). B2C зарегистрирует нового пользователя и отправит в ваше приложение номер loyaltyNumber. Найдите этот номер в примере для объекта JWT ниже.

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

## <a name="next-steps"></a>Дальнейшие действия
* [Azure Active Directory B2C: Secure your RESTful services using client certificates](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) (Azure Active Directory B2C: защита служб RESTful с помощью клиентских сертификатов)

## <a name="optional-download-the-complete-policy-files-and-code"></a>Скачивание полного текста файлов политики и кода (необязательно)
* Мы советуем создать свой сценарий и собственные файлы пользовательской политики, а не использовать эти примеры файлов. Для этого вам будет полезно изучить пошаговое руководство по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* Вы можете загрузить полный исходный код примера для Visual Studio [отсюда](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).