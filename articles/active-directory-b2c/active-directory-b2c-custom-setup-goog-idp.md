---
title: Azure Active Directory B2C. Добавление Google+ в качестве поставщика удостоверений OAuth2 с помощью пользовательских политик
description: Пример использования Google+ в качестве поставщика удостоверений с помощью протокола OAuth2
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: f12bbc2472aa2d83088c7d4d7b8f173744fb36b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C. Добавление Google+ в качестве поставщика удостоверений OAuth2 с помощью пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается, как включить возможность входа для пользователей из учетной записи Google+ с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>предварительным требованиям

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

А именно:

1.  Создание учетной записи в приложении Google+.
2.  Добавление ключа учетной записи Google + в Azure AD B2C.
3.  Добавление поставщика утверждений в политику.
4.  Регистрация поставщика утверждений учетной записи Google+ для пути взаимодействия пользователя
5.  Отправка политики в клиент Azure AD B2C и ее проверка.

## <a name="create-a-google-account-application"></a>Создание приложения для учетной записи Google+
Чтобы использовать Google+ в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Google+ и задать в нем правильные параметры. Приложение Google+ можно зарегистрировать здесь: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1.  Перейдите на сайт [Google Developers Console](https://console.developers.google.com/) и выполните вход с учетной записью Google+.
2.  Щелкните **Create project** (Создать проект), введите значение в поле **Project name** (Имя проекта) и щелкните **Create** (Создать).

3.  Щелкните **меню проектов**.

    ![Учетная запись Google+, меню "Выберите проект"](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Нажмите кнопку **+**.

    ![Учетная запись Google+, создание проекта](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Введите **имя проекта** и нажмите кнопку **Создать**.

    ![Учетная запись Google+, новый проект](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Подождите, пока проект подготовится, и выберите **меню проектов**.

    ![Учетная запись Google+; подождите, пока новый проект будет готов к использованию](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Щелкните имя проекта.

    ![Учетная запись Google+, выбор нового проекта](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  В левой области навигации щелкните **API Manager** (Менеджер API), а затем — **Credentials** (Учетные данные).
9.  Щелкните вкладку **OAuth consent screen** (Экран согласия OAuth) вверху.

    ![Учетная запись Google+, настройка окна запроса доступа OAuth](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Выберите или укажите допустимый **электронный адрес**, заполните поле **Product name** (Название продукта) и нажмите кнопку **Save** (Сохранить).

    ![Google+, учетные данные приложения](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Щелкните **New credentials** (Создать учетные данные) и выберите **OAuth client ID** (Идентификатор клиента OAuth).

    ![Google+, создание учетных данных приложения](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение).

    ![Google+, выбор типа приложения](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  В поле **Name** (Имя) введите имя приложения, затем введите `https://login.microsoftonline.com` в поле **Authorized JavaScript origins** (Авторизованные источники JavaScript) и `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` — в поле **Authorized redirect URIs** (Авторизованные URI перенаправления). Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com). В значении **{клиент}** необходимо учитывать регистр. Нажмите кнопку **Создать**.

    ![Google+, предоставление авторизованных источников JavaScript и URI перенаправления](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Скопируйте значения **идентификатор клиента** и **секрет клиента**. Они необходимы для настройки Google+ в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.

    ![Google+, копирование значений идентификатора и секрета клиента](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Добавление ключа учетной записи Google+ в Azure AD B2C
Федерации с учетными записями Google+ нужен секрет клиента, чтобы учетная запись Google+ смогла установить отношения доверия с Azure AD B2C от имени приложения. Вам необходимо сохранить секрет Google+ в клиенте Azure AD B2C:  

1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Вручную**.
5.  Для параметра **Имя** используйте значение `GoogleSecret`.  
    Префикс `B2C_1A_` может быть добавлен автоматически.
6.  В поле **Secret** (Секрет) введите скопированный выше секрет приложения Google из [консоли разработчиков Google](https://console.developers.google.com/).
7.  Для параметра **Использование ключа** задайте значение **Подпись**.
8.  Нажмите кнопку **Создать**.
9.  Убедитесь, что вы создали ключ `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Добавление поставщика утверждений в политику расширения

Чтобы пользователи выполняли вход с помощью учетной записи Google+, необходимо определить ее в качестве поставщика утверждений. Другими словами, необходимо указать конечную точку, с которой взаимодействует Azure AD B2C. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Определите учетную запись Google+ в качестве поставщика утверждений, добавив узел `<ClaimsProvider>` в файл расширения политики:

1.  Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога. Если вам нужен редактор XML, [попробуйте Visual Studio Code](https://code.visualstudio.com/download) — упрощенный кроссплатформенный редактор.
2.  Найдите раздел `<ClaimsProviders>`.
3.  Добавьте следующий фрагмент XML-кода в элемент `ClaimsProviders` и замените значение `client_id` на ваш идентификатор клиента учетной записи клиента Google+ перед сохранением файла.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Регистрация поставщика утверждений учетной записи Google+ для пути взаимодействия пользователя "Регистрация" или "Вход"

Поставщик удостоверений настроен.  Однако он недоступен ни на одном из экранов регистрации или входа. Добавьте поставщик идентификатора учетной записи Google+ в пути взаимодействия пользователя `SignUpOrSignIn`. Чтобы сделать его доступным, необходимо создать дубликат имеющегося шаблона пути взаимодействия пользователя,  а затем добавить поставщик удостоверений учетной записи Google+:

>[!NOTE]
>
>Если вы скопировали элемент `<UserJourneys>` из основного файла политики в файл расширения (TrustFrameworkExtensions.xml), можно пропустить этот раздел.

1.  Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2.  Найдите элемент `<UserJourneys>` и скопируйте все содержимое узла `<UserJourneys>`.
3.  Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
4.  Вставьте весь скопированный узел `<UserJourney>` как дочерний узел элемента `<UserJourneys>`.

### <a name="display-the-button"></a>Отображение кнопки
Элемент `<ClaimsProviderSelections>` определяет список параметров выбора поставщика утверждений и их порядок.  Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на странице регистрации или входа. Если вы добавите для учетной записи Google+ элемент `<ClaimsProviderSelection>`, при переходе пользователя на страницу отобразится новая кнопка. Для этого:

1.  Найдите узел `<UserJourney>`, содержащий `Id="SignUpOrSignIn"` в скопированном пути пользователя.
2.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
3.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием
Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью Google+ для получения токена.

1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
> * Убедитесь, что для идентификатора `TechnicalProfileReferenceId` задано значение ранее созданного технического профиля (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Откройте колонку **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="test-the-custom-policy-by-using-run-now"></a>Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.

    >[!NOTE]
    >
    >    Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. 
    >    Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).


2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  У вас должна появиться возможность входа с использованием учетной записи Google +.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Необязательно] Регистрация поставщика утверждений учетной записи Google+ для пути взаимодействия пользователя "Изменение профиля"
Вы также можете добавить поставщик удостоверений учетной записи Google+ для пути взаимодействия пользователя `ProfileEdit`. Чтобы сделать его доступным, необходимо повторить последние два шага:

### <a name="display-the-button"></a>Отображение кнопки
1.  Откройте файл расширения политики (например, TrustFrameworkExtensions.xml).
2.  Найдите узел `<UserJourney>`, содержащий `Id="ProfileEdit"` в скопированном пути пользователя.
3.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
4.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием
1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Откройте колонку **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и убедитесь, что он успешно прошел проверку.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Тестирование пользовательской политики изменения профиля с помощью команды "Запустить сейчас"

1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.
2.  Откройте **B2C_1A_ProfileEdit**, отправленную пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  У вас должна появиться возможность входа с использованием учетной записи Google +.

## <a name="download-the-complete-policy-files"></a>Загрузка завершенных файлов политики
Необязательно. Мы советуем создать свой сценарий, используя собственные файлы пользовательской политики, а не эти примеры файлов, после того, как вы ознакомитесь с пошаговым руководством по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
