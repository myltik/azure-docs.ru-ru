---
title: Azure Active Directory B2C. Добавление учетной записи Майкрософт (MSA) в качестве поставщика удостоверений с помощью пользовательских политик
description: Пример использования Майкрософт в качестве поставщика удостоверений с помощью протокола OpenID Connect (OIDC)
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
ms.openlocfilehash: a49e9589322eeb90a713321b4fbe4c4820609f7a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C. Добавление учетной записи Майкрософт (MSA) в качестве поставщика удостоверений с помощью пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается, как включить возможность входа для пользователей из учетной записи Майкрософт (MSA) с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>предварительным требованиям
Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

А именно:

1.  Создание приложения для учетной записи Майкрософт.
2.  Добавление ключа учетной записи Microsoft в Azure AD B2C.
3.  Добавление поставщика утверждений в политику.
4.  Регистрация поставщика утверждений учетной записи Microsoft для пути взаимодействия пользователя.
5.  Отправка политики в клиент Azure AD B2C и ее проверка.

## <a name="create-a-microsoft-account-application"></a>Создание приложения для учетной записи Майкрософт
Чтобы использовать учетную запись Майкрософт в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение для учетной записи Майкрософт и задать в нем правильные параметры. Вам потребуется учетная запись Майкрософт. Если у вас нет учетной записи, создайте ее на сайте [https://www.live.com/](https://www.live.com/).

1.  Перейдите в [Центр регистрации приложений Майкрософт](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) и войдите с помощью своей учетной записи Майкрософт.
2.  Нажмите кнопку **Добавить приложение**.

    ![Учетная запись Майкрософт, добавление приложения](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Укажите **имя** вашего приложения, **адрес электронной почты**, снимите флажок **Мы поможем вам начать работу** и нажмите кнопку **Создать**.

    ![Учетная запись Майкрософт, регистрация приложения](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Скопируйте значение **Идентификатор приложения**. Он необходим для настройки учетной записи Майкрософт в качестве поставщика удостоверений в вашем клиенте.

    ![Учетная запись Майкрософт, копирование значения идентификатора приложения](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Щелкните **Добавление платформы**.

    ![Учетная запись Майкрософт: добавление платформы](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  В списке платформ выберите **Интернет**.

    ![Учетная запись Майкрософт, выбор "Интернет" из списка платформ](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Введите `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` в поле **URI перенаправления** . Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com).

    ![Учетная запись Майкрософт, установка URL-адресов перенаправления](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Щелкните **Создать новый пароль** в разделе **Секреты приложения**. Скопируйте новый пароль с экрана. Он необходим для настройки учетной записи Майкрософт в качестве поставщика удостоверений в вашем клиенте. Данный пароль — важный элемент обеспечения безопасности.

    ![Microsoft учетной записи: создание нового пароля](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Учетная запись Майкрософт, копирование нового пароля](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Установите флажок **Поддержка Live SDK** в разделе **Дополнительные параметры**. Выберите команду **Сохранить**.

    ![Учетная запись Майкрософт: поддержка Live SDK](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Добавление ключа приложения учетной записи Майкрософт в Azure AD B2C
Федерации с учетными записями Майкрософт нужен секрет клиента, чтобы учетная запись Майкрософт смогла установить отношения доверия с Azure AD B2C от имени приложения. Вам необходимо сохранить свой секрет Майкрософт в клиенте Azure AD B2C:   

1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Вручную**.
5.  Для параметра **Имя** используйте значение `MSASecret`.  
    Префикс `B2C_1A_` может быть добавлен автоматически.
6.  В поле **Секрет** введите свой секрет приложения Майкрософт с сайта https://apps.dev.microsoft.com.
7.  Для параметра **Использование ключа** задайте значение **Подпись**.
8.  Нажмите кнопку **Создать**.
9.  Убедитесь, что вы создали ключ `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Добавление поставщика утверждений в политику расширения
Чтобы пользователи выполняли вход с помощью учетной записи Майкрософт, необходимо определить ее в качестве поставщика утверждений. Другими словами, необходимо указать конечную точку, с которой взаимодействует Azure AD B2C. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Определите учетную запись Майкрософт в качестве поставщика утверждений, добавив узел `<ClaimsProvider>` в файл расширения политики:

1.  Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога. Если вам нужен редактор XML, [попробуйте Visual Studio Code](https://code.visualstudio.com/download) — упрощенный кроссплатформенный редактор.
2.  Найдите раздел `<ClaimsProviders>`.
3.  Добавьте в элемент `ClaimsProviders` следующий фрагмент XML-кода:

    ```xml
<ClaimsProvider>
    <Domain>live.com</Domain>
    <DisplayName>Microsoft Account</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="MSA-OIDC">
        <DisplayName>Microsoft Account</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <Metadata>
        <Item Key="ProviderName">https://login.live.com</Item>
        <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="scope">openid profile email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="email" />
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

4.  Замените значение `client_id` на идентификатор клиента приложения учетной записи Майкрософт.

5.  Сохраните файл.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Регистрация поставщика утверждений учетной записи Майкрософт для пути взаимодействия пользователя "Регистрация" или "Вход"

На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Теперь необходимо добавить поставщик удостоверений учетной записи Майкрософт для пути взаимодействия пользователя `SignUpOrSignIn`. Чтобы сделать его доступным, необходимо создать дубликат имеющегося шаблона пути взаимодействия пользователя,  а затем добавить поставщик удостоверений учетной записи Майкрософт:

> [!NOTE]
>
>Если вы уже скопировали элемент `<UserJourneys>` из основного файла политики в файл расширения`TrustFrameworkExtensions.xml`, можно пропустить этот раздел.

1.  Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2.  Найдите элемент `<UserJourneys>` и скопируйте все содержимое узла `<UserJourneys>`.
3.  Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
4.  Вставьте весь скопированный узел `<UserJournesy>` как дочерний узел элемента `<UserJourneys>`.

### <a name="display-the-button"></a>Отображение кнопки
Элемент `<ClaimsProviderSelections>` определяет список параметров выбора поставщика утверждений и их порядок.  Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на странице регистрации или входа. Если вы добавите для учетной записи Майкрософт элемент `<ClaimsProviderSelection>`, при переходе пользователя на страницу отобразится новая кнопка. Для этого:

1.  Найдите узел `<UserJourney>`, содержащий `Id="SignUpOrSignIn"` в скопированном пути пользователя.
2.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
3.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием
Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью Майкрософт для получения токена. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений учетной записи Майкрософт:

1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
>   * Убедитесь, что для идентификатора `TechnicalProfileReferenceId` задано значение ранее созданного технического профиля (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Откройте колонку **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="test-the-custom-policy-by-using-run-now"></a>Тестирование настраиваемой политики с помощью команды "Запустить сейчас"

1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.
> [!NOTE]
>
>Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).
2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  У вас должна появиться возможность входа с использованием учетной записи Майкрософт.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Необязательно] Регистрация поставщика утверждений учетной записи Майкрософт для пути взаимодействия пользователя "Изменение профиля"
Вы также можете добавить поставщик удостоверений учетной записи Майкрософт для пути взаимодействия пользователя `ProfileEdit`. Чтобы сделать его доступным, необходимо повторить последние два шага:

### <a name="display-the-button"></a>Отображение кнопки
1.  Откройте файл расширения политики (например, TrustFrameworkExtensions.xml).
2.  Найдите узел `<UserJourney>`, содержащий `Id="ProfileEdit"` в скопированном пути пользователя.
3.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
4.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием
1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Тестирование пользовательской политики изменения профиля с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.
2.  Откройте **B2C_1A_ProfileEdit**, отправленную пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  У вас должна появиться возможность входа с использованием учетной записи Майкрософт.

## <a name="download-the-complete-policy-files"></a>Загрузка завершенных файлов политики
Необязательно. Мы советуем создать свой сценарий, используя собственные файлы пользовательской политики, а не эти примеры файлов, после того, как вы ознакомитесь с пошаговым руководством по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
