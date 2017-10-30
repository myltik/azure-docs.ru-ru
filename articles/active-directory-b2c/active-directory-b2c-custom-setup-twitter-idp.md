---
title: "Azure Active Directory B2C. Добавление Twitter в качестве поставщика удостоверений OAuth1 с помощью пользовательских политик"
description: "Пример использования Twitter в качестве поставщика удостоверений с помощью протокола OAuth1."
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
ms.date: 10/02/2017
ms.author: yoelh
ms.openlocfilehash: 8d7bd2ed1cba62677a7eff19ba7018cd25a4345c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-using-custom-policies"></a>Azure Active Directory B2C. Добавление Twitter в качестве поставщика удостоверений OAuth1 с помощью пользовательских политик
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается выполнение входа для пользователей из учетной записи Twitter с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Предварительные требования
Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

А именно:

1.  Создание приложения учетной записи Twitter.
2.  Добавление ключа приложения учетной записи Twitter в Azure AD B2C.
3.  Добавление поставщика утверждений в политику.
4.  Регистрация поставщика утверждений учетной записи Twitter для пути взаимодействия пользователя.
5.  Отправка политики в клиент Azure AD B2C и ее проверка.

## <a name="step-1-create-a-twitter-account-application"></a>Шаг 1. Создание приложения учетной записи Twitter
Чтобы использовать Twitter в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, нужно сначала создать приложение Twitter и задать в нем правильные параметры. Приложение Twitter можно зарегистрировать здесь: [https://twitter.com/signup](https://twitter.com/signup)

1.  Перейдите на веб-сайт [Twitter Developers](https://apps.twitter.com/) (Разработчики Twitter), войдите с помощью учетных данных учетной записи Twitter и щелкните **Create New App**(Создать приложение).

    ![Учетная запись Twitter: создание приложения](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2.  Введите **имя** и **описание** для нового приложения в соответствующих полях. Вставьте значение `https://login.microsoftonline.com` в поле **Website** (Веб-сайт). Затем в поле **Callback URL** (URL-адрес обратного вызова) вставьте значение `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Замените {tenant} именем своего клиента (например, contosob2c.onmicrosoft.com). Убедитесь, что используете схему HTTPS. В нижней части страницы прочтите и примите условия. Затем щелкните **Создать приложение Twitter**.

    ![Учетная запись Twitter: добавление нового приложения](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3.  Откройте вкладку **Параметры**, установите флажок **Allow this application to be used to sign in with Twitter** (Разрешить использовать это приложение для входа в Twitter), а затем щелкните **Обновить параметры**.
4.  Откройте вкладку **Ключи и токены доступа** . Запишите значения полей **Consumer Key (API Key)** (Ключ потребителя (ключ API)) и **Consumer secret (API Secret)** (Секрет потребителя (секрет API)).

    ![Учетная запись Twitter: задание свойств приложения](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

> [!NOTE]
>
>Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

## <a name="step-2-add-the-twitter-account-application-key-to-azure-ad-b2c"></a>Шаг 2. Добавление ключа приложения учетной записи Twitter в Azure AD B2C
Федерации с учетными записями Twitter нужен секрет клиента, чтобы учетная запись Twitter могла установить отношение доверия с Azure AD B2C от имени приложения. Секрет клиента для приложения Twitter нужно сохранить в клиенте Azure AD B2C следующим образом.  

1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Вручную**.
5.  Для параметра **Имя** используйте значение `TwitterSecret`.  
    Префикс `B2C_1A_` может быть добавлен автоматически.
6.  В поле **Секрет** введите свой секрет приложения Майкрософт с сайта https://apps.dev.microsoft.com.
7.  Для параметра **Использование ключа** задайте значение **Шифрование**.
8.  Нажмите кнопку **Создать**
9.  Убедитесь, что вы создали ключ `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Шаг 3. Добавление поставщика утверждений в политику расширения

Чтобы пользователи выполняли вход с помощью учетной записи Twitter, необходимо определить учетную запись Twitter в качестве поставщика утверждений. То есть нужно указать конечные точки, с которыми взаимодействует Azure AD B2C. Эти конечные точки предоставляют набор утверждений, которые позволяют Azure AD B2C убедиться, что аутентификацию проходит конкретный пользователь.

Определите Twitter в качестве поставщика утверждений, добавив узел `<ClaimsProvider>` в файл политики расширения.

1.  Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога. 
2.  Найдите раздел `<ClaimsProviders>`.
3.  Добавьте в узел `<ClaimsProviders>` следующий фрагмент кода XML.  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            </OutputClaims>
            <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

4.  Замените значение `client_id` ключом клиента для приложения учетной записи Twitter.

5.  Сохраните файл.

## <a name="step-4-register-the-twitter-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Шаг 4. Регистрация поставщика утверждений для учетной записи Twitter для пути взаимодействия пользователя "Регистрация" или "Вход"
На этом этапе поставщик удостоверений настроен. Однако он недоступен ни на одном из экранов регистрации или входа. Теперь необходимо добавить поставщик удостоверений учетной записи Twitter для пути взаимодействия пользователя `SignUpOrSignIn`.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Шаг 4.1. Создание копии пути взаимодействия пользователя
Чтобы сделать его доступным, создайте дубликат существующего шаблона пути взаимодействия пользователя. Теперь добавьте поставщик удостоверений Twitter.

> [!NOTE]
>
>Если вы скопировали элемент `<UserJourneys>` из основного файла политики в файл расширения (TrustFrameworkExtensions.xml), можно сразу перейти к следующему разделу.

1.  Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2.  Найдите элемент `<UserJourneys>` и скопируйте все содержимое узла `<UserJourneys>`.
3.  Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
4.  Вставьте весь скопированный узел `<UserJournesy>` как дочерний узел элемента `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Шаг 4.2. Отображение кнопки
Элемент `<ClaimsProviderSelections>` определяет список параметров выбора поставщика утверждений и их порядок.  Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на странице регистрации или входа. Если вы добавите для учетной записи Twitter элемент `<ClaimsProviderSelection>`, то при переходе пользователя на страницу отобразится новая кнопка. Для этого:

1.  Найдите узел `<UserJourney>`, содержащий `Id="SignUpOrSignIn"` в скопированном пути пользователя.
2.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
3.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Шаг 4.3. Связывание кнопки с действием
Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью Twitter для получения маркера. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений учетной записи Twitter.

1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    > [!NOTE]
    >
    > * Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
    > * Убедитесь, что для идентификатора `TechnicalProfileReferenceId` задано значение ранее созданного технического профиля (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Шаг 5. Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и щелкните **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Щелкните **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Шаг 6. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"

1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.

    > [!NOTE]
    >
    >Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  У вас должна появиться возможность входа с использованием учетной записи Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-profile-edit-user-journey"></a>Шаг 7. [Необязательно.] Регистрация поставщика утверждений учетной записи Twitter для пути взаимодействия пользователя "Изменение профиля"
Вы также можете добавить поставщик удостоверений учетной записи Twitter для пути взаимодействия пользователя `ProfileEdit`. Чтобы сделать его доступным, повторите шаг 4. Но на этот раз выберите узел `<UserJourney>`, который содержит `Id="ProfileEdit"`. Теперь сохраните, отправьте и протестируйте политику.


## <a name="download-the-complete-policy-files"></a>Загрузка завершенных файлов политики
Необязательно. Мы советуем создать свой сценарий, используя собственные файлы пользовательской политики, а не эти примеры файлов, после того, как вы ознакомитесь с пошаговым руководством по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)