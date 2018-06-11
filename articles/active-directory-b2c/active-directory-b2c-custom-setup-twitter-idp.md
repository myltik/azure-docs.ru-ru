---
title: Добавление Twitter в качестве поставщика удостоверений OAuth1 с помощью пользовательских политик в Azure Active Directory B2C | Документация Майкрософт
description: Использование Twitter в качестве поставщика удостоверений с помощью протокола OAuth1.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6b09bb295d889255dada0cebbb9ded2379d95d23
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34710242"
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C. Добавление Twitter в качестве поставщика удостоверений OAuth1 с помощью пользовательских политик
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается выполнение входа для обладателя учетной записи Twitter с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>предварительным требованиям
Выполните шаги, описанные в статье [Azure Active Directory B2C: начало работы с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-twitter-account-application"></a>Шаг 1. Создание приложения учетной записи Twitter
Чтобы использовать Twitter в качестве поставщика удостоверений в Azure Active Directory B2C (Azure AD B2C), нужно сначала создать приложение Twitter и задать в нем правильные параметры. Чтобы зарегистрировать приложение Twitter, откройте [страницу регистрации Twitter](https://twitter.com/signup).

1. Перейдите на веб-сайт [Twitter Developers](https://apps.twitter.com/) (Разработчики Twitter), войдите с помощью учетных данных учетной записи Twitter и выберите **Create New App** (Создать приложение).

    ![Учетная запись Twitter: создание приложения](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. В окне **Create an application** (Создать приложение) сделайте следующее:
 
    a. Введите **имя** и **описание** для нового приложения в соответствующих полях. 

    Б. В поле **Веб-сайт** вставьте **https://login.microsoftonline.com**. 

    c. В поле **URL-адрес обратного вызова** вставьте **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Замените заполнитель {*клиент*} именем своего клиента (например, contosob2c.onmicrosoft.com). Убедитесь, что используете схему HTTPS. 

    d. В нижней части страницы прочитайте и примите условия соглашения, а затем выберите **Create your Twitter application** (Создать приложение Twitter).

    ![Учетная запись Twitter: добавление нового приложения](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. В окне **B2C demo** (Демонстрация B2C) выберите **Settings** (Параметры), установите флажок **Allow this application to be used to sign in with Twitter** (Разрешить использовать это приложение для входа в Twitter), а затем выберите **Update Settings** (Обновить параметры).

4. Выберите **Keys and Access Tokens** (Ключи и маркеры доступа) и запишите значения полей **Consumer Key (API Key)** (Ключ клиента (ключ API)) и **Consumer Secret (API Secret)** (Секрет клиента (секрет API)).

    ![Учетная запись Twitter: задание свойств приложения](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Шаг 2. Добавление ключа приложения учетной записи Twitter в Azure AD B2C
Федерации с учетными записями Twitter нужен секрет клиента, чтобы учетная запись Twitter могла установить отношение доверия с Azure AD B2C от имени приложения. Чтобы сохранить секрет клиента для приложения Twitter в клиенте Azure AD B2C, сделайте следующее: 

1. В клиенте Azure AD B2C выберите **B2C Settings** (Параметры B2C)  >  **Identity Experience Framework**.

2. Чтобы просмотреть доступные в клиенте ключи, выберите **Ключи политики**.

3. Выберите **Добавить**.

4. В поле **Параметры** выберите **Вручную**.

5. В поле **Имя** выберите **TwitterSecret**.  
    Префикс *B2C_1A_* может быть добавлен автоматически.

6. В поле **Секрет** введите секрет приложения Майкрософт, полученный на [портале регистрации приложения](https://apps.dev.microsoft.com).

7. Для параметра **Использование ключа** задайте значение **Шифрование**.

8. Нажмите кнопку **Создать**.

9. Убедитесь, что вы создали ключ `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Шаг 3. Добавление поставщика утверждений в политику расширения

Чтобы пользователи выполняли вход с помощью учетной записи Twitter, необходимо определить учетную запись Twitter в качестве поставщика утверждений. Для этого нужно указать конечные точки, с которыми взаимодействует Azure AD B2C. Эти конечные точки предоставляют набор утверждений, которые позволяют Azure AD B2C убедиться, что аутентификацию проходит конкретный пользователь.

Определите Twitter в качестве поставщика утверждений, добавив узел `<ClaimsProvider>` в файл политики расширения.

1. В рабочей папке откройте файл политики расширения *TrustFrameworkExtensions.xml*. 

2. Найдите раздел `<ClaimsProviders>`.

3. В узел `<ClaimsProviders>` добавьте следующий фрагмент кода XML:  

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

4. Замените значение *client_id* ключом клиента для приложения учетной записи Twitter.

5. Сохраните файл.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Шаг 4. Регистрация поставщика утверждений для учетной записи Twitter для пути взаимодействия пользователя "Регистрация" или "Вход"
Итак, вы настроили поставщик удостоверений. Но пока он недоступен в окнах регистрации или входа. Теперь необходимо добавить поставщик удостоверений учетной записи Twitter для пути взаимодействия пользователя `SignUpOrSignIn`.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Шаг 4.1. Создание копии пути взаимодействия пользователя
Чтобы подготовить путь взаимодействия пользователя, создайте копию имеющегося шаблона пути взаимодействия пользователя и добавьте в него поставщик удостоверений Twitter.

>[!NOTE]
>Если вы скопировали элемент `<UserJourneys>` из основного файла политики в файл расширения *TrustFrameworkExtensions.xml*, можно перейти к следующему разделу.

1. Откройте базовый файл политики (например, TrustFrameworkBase.xml).

2. Найдите элемент `<UserJourneys>`, выделите все содержимое узла `<UserJourney>`, а затем выберите действие **Вырезать** для перемещения выделенного текста в буфер обмена.

3. Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если такой элемент не существует, добавьте его.

4. Вставьте все содержимое узла `<UserJourney>`, которое вы поместили в буфер обмена на шаге 2, в элемент `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Шаг 4.2. Отображение кнопки
Элемент `<ClaimsProviderSelections>` определяет список параметров выбора поставщика утверждений и их порядок. Узел `<ClaimsProviderSelection>` действует как кнопка поставщика удостоверений на странице регистрации или входа. Если вы добавите узел `<ClaimsProviderSelection>` для учетной записи Twitter, на странице входа пользователя отобразится новая кнопка. Чтобы добавить этот элемент, сделайте следующее:

1. Найдите узел `<UserJourney>`, который содержит `Id="SignUpOrSignIn"` в скопированном пути взаимодействия пользователя.

2. Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.

3. В элемент `<ClaimsProviderSelections>` добавьте следующий фрагмент кода XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Шаг 4.3. Связывание кнопки с действием
Теперь у вас есть кнопка и ее нужно связать с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью Twitter для получения маркера. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений учетной записи Twitter.

1. В узле `<UserJourney>` найдите узел `<OrchestrationStep>`, содержащий `Order="2"`.
2. В элемент `<ClaimsExchanges>` добавьте следующий фрагмент кода XML:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
    >* Убедитесь, что для параметра `TechnicalProfileReferenceId` задан идентификатор ранее созданного технического профиля (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Шаг 5. Отправка политики в клиент
1. На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и выберите **Azure AD B2C**.

2. Выберите **Инфраструктура процедур идентификации**.

3. Выберите **Все политики**.

4. Щелкните **Отправить политику**.

5. Установите флажок **Перезаписать политику, если она существует**.

6. Отправьте файлы *TrustFrameworkBase.xml* и *TrustFrameworkExtensions.xml*. Убедитесь, что они прошли проверку.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Шаг 6. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"

1. Откройте **параметры Azure AD B2C** и выберите **Identity Experience Framework**.

    >[!NOTE]
    >Для использования команды Run now (Запустить сейчас) необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

2. Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны, а затем выберите **Run Now** (Запустить сейчас).  
    Теперь вы можете войти с использованием учетной записи Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Шаг 7. Регистрация поставщика утверждений учетной записи Twitter в пути взаимодействия пользователя "Изменение профиля" (необязательно)
Возможно, вы захотите добавить поставщик удостоверений учетной записи Twitter в путь взаимодействия пользователя `ProfileEdit`. Чтобы сделать этот путь взаимодействия пользователя доступным, повторите шаг 4. Но на этот раз выберите узел `<UserJourney>`, который содержит `Id="ProfileEdit"`. Теперь сохраните, отправьте и протестируйте политику.


## <a name="optional-download-the-complete-policy-files"></a>Скачивание полного текста файлов политики (необязательно)
После того как вы ознакомитесь с [пошаговым руководством по началу работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md), рекомендуем создать свой сценарий, используя собственные файлы пользовательской политики. Для справки мы предоставили [образцы файлов политики](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
