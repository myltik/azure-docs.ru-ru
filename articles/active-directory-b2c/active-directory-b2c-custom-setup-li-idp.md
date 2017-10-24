---
title: "Azure Active Directory B2C. Добавление LinkedIn в качестве поставщика удостоверений OAuth2 с помощью пользовательских политик"
description: "В статье описывается процедура настройки приложения LinkedIn с использованием протокола OAuth2 и пользовательских политик"
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
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C. Добавление LinkedIn в качестве поставщика удостоверений с помощью пользовательских политик
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается выполнение входа для пользователей из учетной записи LinkedIn с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Предварительные требования
Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

А именно:
1.  Создание приложения учетной записи LinkedIn.
2.  Добавление ключа приложения учетной записи LinkedIn в Azure AD B2C.
3.  Добавление поставщика утверждений в политику.
4.  Включение поставщика утверждений учетной записи LinkedIn в путь взаимодействия пользователя
5.  Отправка политики в клиент Azure AD B2C и проверка этой политики.

## <a name="step-1-create-a-linkedin-account-application"></a>Шаг 1. Создание приложения учетной записи LinkedIn
Чтобы использовать LinkedIn в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо создать приложение LinkedIn и задать в нем правильные параметры. Приложение LinkedIn вы можете зарегистрировать здесь: [https://LinkedIn.com/signup](https://LinkedIn.com/signup)

1.  Откройте веб-сайт [управления приложением LinkedIn](https://www.linkedin.com/secure/developer?newapp=), войдите с учетными данными учетной записи LinkedIn и щелкните **Create Application** (Создать приложение).

    ![Учетная запись LinkedIn — создание приложения](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  Введите **Company Name** (Название компании), укажите описательные значения **Name** (Имя) и **Description** (Описание) для нового приложения.
    2.  Отправьте файл **Application logo** (Логотип приложения)
    3.  Выберите значение **Application Use** (Использование приложения)
    4.  Вставьте значение `https://login.microsoftonline.com` в поле **Website URL** (URL-адрес веб-сайта).
    5.  Введите **Business email** (Рабочий адрес электронной почты) и **Business Phone** (Рабочий телефон).
    6.  В нижней части страницы прочтите условия использования и подтвердите свое согласие. Теперь нажмите кнопку **Submit**(Отправить).

    ![Учетная запись LinkedIn — настройка свойств приложения](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  В меню выберите пункт **Authentication** (Аутентификация). Сохраните значения **Client ID** (Идентификатор клиента) и **Client Secret** (Секрет клиента).

    В поле **Authorized redirect URLs** (Авторизованные URL-адреса перенаправления) вставьте значение `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Замените заполнитель {tenant} именем своего клиента (например, contosob2c.onmicrosoft.com). Выберите схему HTTPS и нажмите кнопку **Add** (Добавить).

    ![Учетная запись LinkedIn — настройка авторизованных URL-адресов перенаправления](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этого секрета и не распространяйте его вместе с вашим приложением.

4.  Откройте вкладку **Settings** (Параметры), в поле **Application status** (Состояние приложения) укажите значение **Live** (Используется) и нажмите кнопку **Update** (Обновить).

    ![Учетная запись LinkedIn — изменение состояния приложения](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Шаг 2. Добавление ключа приложения LinkedIn в Azure AD B2C
Чтобы создать федерацию с учетными записями LinkedIn, учетная запись LinkedIn должна от имени приложения доверять Azure AD B2C. Для этого нам нужен секрет клиента. Секрет приложения LinkedIn нужно сохранить в клиенте Azure AD B2C следующим образом.  

1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Вручную**.
5.  Для параметра **Имя** используйте значение `LinkedInSecret`.  
    Префикс `B2C_1A_` может быть добавлен автоматически.
6.  В поле **Секрет** введите свой секрет приложения LinkedIn, полученный с сайта https://apps.dev.microsoft.com.
7.  Для параметра **Использование ключа** задайте значение **Шифрование**.
8.  Нажмите кнопку **Создать** 
9.  Убедитесь, что вы создали ключ `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Шаг 3. Добавление поставщик утверждений в политику расширения
Чтобы пользователи выполняли вход с помощью учетной записи LinkedIn, необходимо определить LinkedIn в качестве поставщика утверждений. То есть нужно указать конечную точку, с которой взаимодействует Azure AD B2C. Эта конечная точка предоставляет набор утверждений, которые позволяют Azure AD B2C убедиться, что аутентификацию проходит конкретный пользователь.

Чтобы определить LinkedIn в качестве поставщика утверждений, добавьте узел `<ClaimsProvider>` в файл политики расширения.

1.  Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога. 
2.  Найдите раздел `<ClaimsProviders>`.
3.  Добавьте в узел `<ClaimsProviders>` следующий фрагмент XML-кода:  

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

4.  Замените значение `client_id` идентификатором клиента для приложения LinkedIn.
5.  Сохраните файл.

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Шаг 4. Регистрация поставщика утверждений для учетной записи LinkedIn в путях взаимодействия пользователя "Регистрация" или "Вход"
На этом этапе поставщик удостоверений настроен. Однако он недоступен ни на одном из экранов регистрации или входа. Теперь необходимо добавить поставщик удостоверений учетной записи LinkedIn для пути взаимодействия пользователя `SignUpOrSignIn`.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Шаг 4.1. Создание копии пути взаимодействия пользователя
Чтобы сделать его доступным, создайте дубликат существующего шаблона пути взаимодействия пользователя. Теперь добавьте поставщик удостоверений LinkedIn:

> [!NOTE]
>
>Если вы скопировали элемент `<UserJourneys>` из основного файла политики в файл расширения (TrustFrameworkExtensions.xml), можно пропустить этот раздел.

1.  Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2.  Найдите элемент `<UserJourneys>` и скопируйте все содержимое узла `<UserJourneys>`.
3.  Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
4.  Вставьте весь скопированный узел `<UserJournesy>` как дочерний узел элемента `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Шаг 4.2. Отображение кнопки
Элемент `<ClaimsProviderSelections>` определяет список параметров выбора поставщика утверждений и их порядок.  Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на странице регистрации или входа. Если вы добавите для учетной записи LinkedIn элемент `<ClaimsProviderSelection>`, при переходе пользователя на страницу отобразится новая кнопка. Для этого:

1.  Найдите узел `<UserJourney>`, содержащий `Id="SignUpOrSignIn"` в скопированном пути пользователя.
2.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
3.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>Шаг 4.3. Связывание кнопки с действием
Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В нашем примере действие — это взаимодействие Azure AD B2C с учетной записью LinkedIn для получения маркера. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений учетной записи LinkedIn.

1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
    > * Убедитесь, что для идентификатора `TechnicalProfileReferenceId` задано значение ранее созданного технического профиля (LinkedIn-OAUTH).

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
3.  Вы можете войти с использованием учетной записи LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>Шаг 7. [Необязательно] Регистрация поставщика утверждений учетной записи LinkedIn в пути взаимодействия пользователя "Изменение профиля"
Вы также можете добавить поставщик удостоверений учетной записи LinkedIn в путь взаимодействия пользователя `ProfileEdit`. Чтобы сделать его доступным, повторите шаг 4. Но на этот раз выберите узел `<UserJourney>`, который содержит `Id="ProfileEdit"`. Теперь сохраните, отправьте и протестируйте политику.

## <a name="download-the-complete-policy-files"></a>Загрузка завершенных файлов политики
Необязательно. Мы советуем создать свой сценарий, используя собственные файлы пользовательской политики, а не эти примеры файлов, после того, как вы ознакомитесь с пошаговым руководством по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)