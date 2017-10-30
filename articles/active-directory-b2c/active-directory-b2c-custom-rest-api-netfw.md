---
title: "Azure Active Directory B2C. Интеграция обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных"
description: "Пример интеграции обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных"
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
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: da7b8326a89fc6eb0bc3e7365c522d4a3adc809b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Azure Active Directory B2C. Интеграция обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных
Инфраструктура процедур идентификации, лежащая в основе Azure Active Directory B2C (Azure AD B2C), позволяет выполнять интеграцию с RESTful API в пути взаимодействия пользователя. В этом пошаговом руководстве будет показано, как B2C взаимодействует со службами RESTful (веб-API) .NET Framework.

## <a name="introduction"></a>Введение
Azure AD B2C позволяет добавлять собственную бизнес-логику в пути взаимодействия пользователя с помощью вызова службы RESTful. Инфраструктура процедур идентификации отправляет данные в службу RESTful в коллекции **входящих утверждений** и получает данные из RESTful в коллекции **исходящих утверждений**. Благодаря интеграции со службой RESTful вы можете:

* Проверять входные данные пользователя, чтобы предотвратить сохранение в Azure AD неправильно сформированных данных. Если значение, полученное от пользователя, является недопустимым, служба RESTful возвращает сообщение об ошибке, которое инструктирует пользователя предоставить запись. Например, вы можете проверить, что адрес электронной почты, предоставленный пользователем, существует в базе данных ваших клиентов.
* Перезаписывать входящие утверждения. Например, если пользователь вводит имя в нижнем или в верхнем регистре, вы можете отформатировать имя и сделать прописной только первую букву.
* Дополнять данные пользователя за счет дальнейшей интеграции с корпоративным бизнес-приложением. Служба RESTful может получать адреса электронной почты пользователей, запрашивать базу данных клиентов и возвращать номер лояльности пользователя обратно в B2C. Возвратные утверждения могут храниться в учетной записи AAD пользователя, проверяться в следующих **действиях оркестрации** или быть включены в маркер доступа.
* Запускать пользовательскую бизнес-логику: отправлять push-уведомления, обновлять корпоративные базы данных, запускать процесс миграции пользователей, управлять разрешениями, выполнять аудит и многое другое.

Интеграция со службами RESTful может быть спроектирована как обмен утверждениями или как технический профиль проверки:

* **Технический профиль проверки**. Вызов службы RESTful происходит в файле ValidationTechnicalProfile данного технического профиля. Технический профиль проверки проверяет данные, предоставленные пользователем, в начале пути взаимодействия пользователя. С помощью технического профиля проверки вы можете:
  * отправлять входящие утверждения;
  * проверять входящие утверждения и инициировать пользовательские сообщения об ошибках;
  * отправлять назад исходящие утверждения.

* **Обмен утверждениями**. Аналогично предыдущему, но происходит на этапе оркестрации. Может только:
   * отправлять входящие утверждения;
   * отправлять назад исходящие утверждения.

## <a name="restful-walkthrough"></a>Пошаговое руководство по RESTful
В этом пошаговом руководстве вы разработаете веб-API .NET Framework, который проверяет входные данные пользователя и предоставляет его номер лояльности. Например, ваше приложение может предоставить доступ к "платиновым преимуществам" на основе номера лояльности.

Рассматриваемые действия:
*   разработка службы RESTful (веб-API .NET Framework);
*   использование службы RESTful в пути взаимодействия пользователя;
*   отправка входящих утверждений и чтение их в коде;
*   проверка имени пользователя;
*   обратная отправка номера лояльности; 
*   добавление номера лояльности в веб-маркер JSON (JWT).

## <a name="prerequisites"></a>Предварительные требования
Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Шаг 1. Создание веб-API ASP.NET
1.  Создайте проект в Visual Studio, последовательно выбрав пункты **Файл > Создать > Проект**.
2.  В диалоговом окне **Новый проект** последовательно выберите пункты **Visual C# > Интернет > Веб-приложение ASP.NET (.NET Framework)**.
3.  Присвойте имя приложению, например Contoso.AADB2C.API, а затем нажмите кнопку **ОК**.

    ![Создание проекта Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4.  Выберите шаблон **веб-API** или **приложения API Azure**.
5.  Обязательно выберите **Без аутентификации** в параметрах проверки подлинности.

    ![Выбор шаблона веб-API](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

6.  Нажмите кнопку **ОК**, чтобы создать проект.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Шаг 2. Подготовка конечной точки REST API

### <a name="step-21-add-data-models"></a>Шаг 2.1. Добавление моделей данных
Модели представляют данные входящих и исходящих утверждений в службе RESTful. Ваш код считывает входные данные, десериализуя модель входящих утверждений из строки JSON в объект C# (ваша модель). Веб-API ASP.NET автоматически десериализует модель исходящих утверждений обратно в JSON, а затем записывает сериализованные данные в текст сообщения HTTP-ответа. Давайте начнем с создания модели, представляющей входящие утверждения.

1.  Если обозреватель решений не отображается, щелкните меню **Просмотр** и выберите **Обозреватель решений**. В обозревателе решений щелкните правой кнопкой мыши папку Models. В контекстном меню выберите **Добавить**, а затем выберите **Класс**.

    ![Добавление модели](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

2.  Назовите класс `InputClaimsModel` и добавьте следующие свойства в класс `InputClaimsModel`.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

3.  Создайте модель `OutputClaimsModel` и добавьте следующие свойства в класс `OutputClaimsModel`.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

4.  Создайте еще одну модель `B2CResponseContent`. Эта модель передает сообщения об ошибках проверки вводных данных. Добавьте следующие свойства в класс `B2CResponseContent`, укажите отсутствующие ссылки и сохраните файл.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Шаг 2.2. Добавление контроллера
В веб-API _контроллер_ — это объект, который обрабатывает HTTP-запросы. Добавим контроллер, который возвращает исходящие утверждения или, если первое имя недопустимо, выдает сообщение об ошибке HTTP "Конфликт".

1.  В **обозревателе решений** щелкните правой кнопкой мыши папку Controllers. Щелкните **Добавить**, а затем выберите **Контроллер**.

    ![Добавление нового контроллера](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2.  В диалоговом окне **Добавление шаблона** выберите **Контроллер веб-API — пустой**. Щелкните **Добавить**.

    !![Щелкните элемент "Контроллер веб-API 2 — пустой".](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3.  В диалоговом окне **Добавление контроллера** назовите контроллер `IdentityController` и нажмите кнопку **Добавить**.

    ![Ввод имени контроллера](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    В результате формирования шаблона создается файл с именем IdentityController.cs в папке Controllers.

4.  Если этот файл еще не открыт, дважды щелкните его. Замените содержимое этого файла приведенным ниже кодом.

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If not data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create output claims object and set the loyalty number with random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-to-azure"></a>Шаг 3. Публикация в Azure
1.  В **обозревателе решений** щелкните правой кнопкой мыши проект **Contoso.AADB2C.API** и выберите **Опубликовать**.

    ![Публикация в службе приложений Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2.  Выберите **Служба приложений Microsoft Azure** и нажмите кнопку **Опубликовать**.

    ![Создание службы приложений Microsoft Azure](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Откроется диалоговое окно **Создать службу приложений**, с помощью которого вы можете создать все ресурсы Azure, необходимые для запуска веб-приложения ASP.NET в Azure.

> [!NOTE]
>Дополнительные сведения см. в статье [Создание веб-приложения ASP.NET в Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3.  В поле **Имя веб-приложения** введите уникальное имя (допустимые символы: `a-z`, `0-9` и `-`). URL-адрес веб-приложения: `http://<app_name>.azurewebsites.NET`, где `<app_name>` — имя веб-приложения. Можно принять имя, созданное автоматически, если оно является уникальным.

    Нажмите кнопку **Создать**, чтобы начать создавать ресурсы Azure.

    ![Свойства службы приложения Provite](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4.  По завершении работы мастера веб-приложение ASP.NET будет опубликовано в Azure, запущено в браузере по умолчанию и будет скопирован URL-адрес.

## <a name="step-4-add-the-new-claim-loyaltynumber-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Шаг 4. Добавление нового утверждения `loyaltyNumber` в схему файла TrustFrameworkExtensions.xml
Утверждение `loyaltyNumber` еще нигде не определено в схеме. Поэтому добавьте определение внутрь элемента `<BuildingBlocks>`. Этот элемент можно найти в начале файла TrustFrameworkExtensions.xml.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-adding-claims-provider"></a>Шаг 5. Добавление поставщика утверждений 
Каждый поставщик утверждений должен иметь один или несколько технических профилей, которые определяют конечные точки и протоколы, необходимые для связи с этим поставщиком утверждений. Поставщик утверждений может иметь несколько технических профилей по различным причинам. Например, может быть определено несколько технических профилей, так как рассматриваемый поставщик утверждений поддерживает несколько протоколов, различные конечные точки с разными возможностями или выпускает различные утверждения на разных уровнях безопасности. Это может быть приемлемым для выдачи конфиденциальных утверждений в одном пути взаимодействия пользователя, а в другом нет. 

Следующий фрагмент XML-кода содержит узел `ClaimsProvider` с двумя `TechnicalProfile`:
* `<TechnicalProfile Id="REST-API-SignUp">` определяет службу RESTful. `Proprietary` описан как протокол для поставщика на основе RESTful. Элемент `InputClaims` определяет утверждения, которые отправляются из B2C в службу REST. В этом примере содержимое утверждения `givenName` отправляется в службу REST в качестве `firstName`, содержимое утверждения `surename` отправляется в службу REST в качестве `lastName`, а `email` отправляется как есть. Элемент `OutputClaims` определяет утверждения, извлекаемые из службы RESTful обратно в B2C.

* `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` добавляет технический профиль проверки в существующий технический профиль (определяемый в базовой политике). Во время регистрации технический профиль проверки вызывает упомянутый выше технический профиль. Если эта служба возвращает сообщение об ошибке HTTP 409 (конфликт), сообщение об ошибке отправляется пользователю. 

Найдите узел `<ClaimsProviders>` и добавьте следующий фрагмент XML-кода в узел `<ClaimsProviders>`:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://yourdomain.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Шаг 6. Добавление утверждения `loyaltyNumber` в файл политики проверяющей стороны для отправки утверждения приложению
Необходимо изменить файл проверяющей стороны SignUpOrSignIn.xml, а также элемент `<TechnicalProfile Id="PolicyProfile">` для добавления `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

После добавления нового утверждения `RelyingParty` должен выглядеть следующим образом:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Шаг 7. Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Щелкните **Все политики**. 
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.
7.  Повторите последний шаг и отправьте файл SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Шаг 8. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.

> [!NOTE]
>
>Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).


2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.

    ![](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3.  Попробуйте ввести Test в поле **Given Name** (Имя). В верхней части страницы B2C отобразится сообщение об ошибке.

    ![Тестирование политики](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Попробуйте ввести в поле **Given Name** (Имя) другое имя (не Test). B2C зарегистрирует нового пользователя и отправит в ваше приложение номер loyaltyNumber. Запишите номер в этом JWT.

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
* Мы советуем создать свой сценарий и собственные файлы пользовательской политики, а не использовать эти примеры файлов. Для этого вам будет полезно изучить пошаговое руководство по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)
* Вы можете загрузить полный код примера для решения Visual Studio [отсюда](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Дальнейшие действия
1.  [Azure Active Directory B2C: Secure your RESTful services using HTTP basic authentication](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Azure Active Directory B2C: защита служб RESTful с использованием обычной проверки подлинности HTTP)
2.  [Azure Active Directory B2C: Secure your RESTful services using client certificates](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) (Azure Active Directory B2C: защита служб RESTful с помощью клиентских сертификатов)