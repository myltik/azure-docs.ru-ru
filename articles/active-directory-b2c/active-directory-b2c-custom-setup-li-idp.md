---
title: Добавление ADFS в качестве поставщика удостоверений OAuth2 с помощью пользовательских политик в Azure Active Directory B2C | Документация Майкрософт
description: В статье описано, как настроить приложение LinkedIn с помощью протокола OAuth2 и пользовательских политик.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0544daf310d40ea419cb6a46cbbf308ebc924715
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709331"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C. Добавление LinkedIn в качестве поставщика удостоверений с помощью пользовательских политик
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается выполнение входа для обладателей учетной записи LinkedIn с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>предварительным требованиям
Выполните шаги, описанные в статье [Azure Active Directory B2C: начало работы с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-a-linkedin-account-application"></a>Шаг 1. Создание приложения учетной записи LinkedIn
Чтобы использовать LinkedIn в качестве поставщика удостоверений в Azure Active Directory B2C (Azure AD B2C), следует создать приложение LinkedIn и задать в нем правильные параметры. Чтобы зарегистрировать приложение LinkedIn, перейдите на [страницу регистрации LinkedIn](https://LinkedIn.com/signup).

1. Откройте веб-сайт [управления приложением LinkedIn](https://www.linkedin.com/secure/developer?newapp=), войдите с учетными данными учетной записи LinkedIn и выберите **Create Application** (Создать приложение).

    ![Учетная запись LinkedIn — создание приложения](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. На странице **Create a New Application** (Создать приложение) выполните описанные ниже действия.

    a. Введите значение в поле **Company Name** (Название компании), укажите описательные значения **Name** (Имя) для компании и **Description** (Описание) для нового приложения.

    Б. Отправьте файл в разделе **Application logo** (Логотип приложения).

    c. Выберите значение для **Application Use** (Использование приложения).

    d. В поле **URL-адрес веб-сайта** вставьте **https://login.microsoftonline.com**.

    д. Введите значения в поля **Business email** (Рабочий адрес электронной почты) и **Business Phone** (Рабочий телефон).

    f. В нижней части страницы прочтите условия использования и примите их, а затем щелкните **Submit** (Отправить).

    ![Учетная запись LinkedIn — настройка свойств приложения](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Выберите значение для параметра **Authentication** (Аутентификация) и запишите значения **Client ID** (Идентификатор клиента) и **Client Secret** (Секрет клиента).

4. В поле **Авторизованные URL-адреса перенаправления** вставьте **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Замените заполнитель {*клиент*} именем своего клиента (например, contosob2c.onmicrosoft.com). Убедитесь, что используете схему HTTPS. 

    ![Учетная запись LinkedIn — настройка авторизованных URL-адресов перенаправления](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

5. Выберите **Добавить**.

6. Выберите элемент **Settings** (Настройки), в поле **Application status** (Состояние приложения) выберите значение **Live** (Активно) и щелкните **Update** (Обновить).

    ![Учетная запись LinkedIn — изменение состояния приложения](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Шаг 2. Добавление ключа приложения LinkedIn в Azure AD B2C
Чтобы создать федерацию с учетными записями LinkedIn, учетная запись LinkedIn должна от имени приложения доверять Azure AD B2C. Для этого нам нужен секрет клиента. Чтобы сохранить секрет приложения LinkedIn в клиенте Azure AD B2C, сделайте следующее.  

1. В клиенте Azure AD B2C выберите **B2C Settings** (Параметры B2C)  >  **Identity Experience Framework**.

2. Чтобы просмотреть доступные в клиенте ключи, выберите **Ключи политики**.

3. Выберите **Добавить**.

4. В поле **Параметры** выберите **Отправить**.

5. В поле **Имя** введите **B2cRestClientCertificate**.  
    Префикс *B2C_1A_* может быть добавлен автоматически.

6. В поле **Секрет** введите секрет приложения LinkedIn, полученный на [портале регистрации приложения](https://apps.dev.microsoft.com).

7. Для параметра **Использование ключа** задайте значение **Шифрование**.

8. Нажмите кнопку **Создать**. 

9. Проверьте, создан ли ключ `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Шаг 3. Добавление поставщика утверждений в политику расширения
Чтобы пользователи выполняли вход с помощью учетной записи LinkedIn, следует определить LinkedIn в качестве поставщика утверждений. Для этого нужно указать конечные точки, с которыми взаимодействует Azure AD B2C. Эти конечные точки предоставляют набор утверждений, которые позволяют Azure AD B2C убедиться, что аутентификацию проходит конкретный пользователь.

Чтобы определить LinkedIn в качестве поставщика утверждений, добавьте узел `<ClaimsProvider>` в файл политики расширения.

1. В рабочей папке откройте файл политики расширения *TrustFrameworkExtensions.xml*. 

2. Найдите элемент `<ClaimsProviders>`.

3. В элемент `<ClaimsProviders>` добавьте следующий фрагмент кода XML: 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4. Заполнитель *client_id* замените идентификатором клиента для приложения LinkedIn.

5. Сохраните файл.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Шаг 4. Регистрация поставщика утверждений для учетной записи LinkedIn
Итак, вы настроили поставщик удостоверений. Но пока он недоступен в окнах регистрации или входа. Теперь поставщик удостоверений учетной записи LinkedIn следует добавить в путь взаимодействия пользователя `SignUpOrSignIn`.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Шаг 4.1. Создание копии пути взаимодействия пользователя
Чтобы подготовить путь взаимодействия пользователя, создайте копию существующего шаблона пути взаимодействия пользователя и добавьте в него поставщик удостоверений LinkedIn.

>[!NOTE]
>Если вы скопировали элемент `<UserJourneys>` из основного файла политики в файл расширения *TrustFrameworkExtensions.xml*, можно пропустить этот раздел.

1. Откройте базовый файл политики (например, TrustFrameworkBase.xml).

2. Найдите элемент `<UserJourneys>`, выделите все содержимое узла `<UserJourney>`, а затем выберите действие **Вырезать** для перемещения выделенного текста в буфер обмена.

3. Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если такой элемент не существует, добавьте его.

4. Вставьте все содержимое узла `<UserJourney>`, которое вы поместили в буфер обмена на шаге 2, в элемент `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Шаг 4.2. Отображение кнопки
Элемент `<ClaimsProviderSelections>` определяет список параметров выбора поставщика утверждений и их порядок. Узел `<ClaimsProviderSelection>` действует как кнопка поставщика удостоверений на странице регистрации или входа. Если вы добавите узел `<ClaimsProviderSelection>` для учетной записи LinkedIn, на странице входа пользователя появится новая кнопка. Чтобы добавить этот элемент, сделайте следующее:

1. Найдите узел `<UserJourney>`, который содержит `Id="SignUpOrSignIn"` в скопированном пути взаимодействия пользователя.

2. Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.

3. В элемент `<ClaimsProviderSelections>` добавьте следующий фрагмент кода XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Шаг 4.3. Связывание кнопки с действием
Теперь у вас есть кнопка и ее нужно связать с действием. В нашем примере действием является обмен данными между Azure AD B2C и учетной записью LinkedIn для получения маркера. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений учетной записи LinkedIn.

1. В узле `<UserJourney>` найдите узел `<OrchestrationStep>`, содержащий `Order="2"`.

2. В элемент `<ClaimsExchanges>` добавьте следующий фрагмент кода XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
    >* Убедитесь, что для параметра `TechnicalProfileReferenceId` задан идентификатор ранее созданного технического профиля (LinkedIn-OAUTH).

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
    Теперь вы можете войти с помощью учетной записи LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Шаг 7 (необязательно). Регистрация поставщика утверждений учетной записи LinkedIn в пути взаимодействия пользователя ProfileEdit
Возможно, вы захотите добавить поставщик удостоверений учетной записи LinkedIn в путь взаимодействия пользователя `ProfileEdit`. Чтобы сделать этот путь взаимодействия пользователя доступным, повторите шаг 4. Но на этот раз выберите узел `<UserJourney>`, который содержит `Id="ProfileEdit"`. Теперь сохраните, отправьте и протестируйте политику.

## <a name="optional-download-the-complete-policy-files"></a>Скачивание полного текста файлов политики (необязательно)
После того как вы ознакомитесь с [пошаговым руководством по началу работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md), рекомендуем создать свой сценарий, используя собственные файлы пользовательской политики. Для справки мы предоставили [образцы файлов политики](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
