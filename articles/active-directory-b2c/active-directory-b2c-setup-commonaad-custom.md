---
title: Azure Active Directory B2C. Добавление мультитенантного поставщика удостоверений Azure AD с помощью пользовательских политик | Документация Майкрософт
description: Azure Active Directory B2C. Добавление мультитенантного поставщика удостоверений Azure AD с помощью пользовательских политик.
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: d5e5ab1262a9d33fcf34cce91113f39c8c8936f4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200524"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C. Предоставление пользователям возможности входить в мультитенантный поставщик удостоверений Azure AD с помощью пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается настройка входа для пользователей с помощью мультитенантной конечной точки Azure Active Directory (Azure AD) через [пользовательских политик](active-directory-b2c-overview-custom.md). Это позволяет клиентам мультитенантного поставщика Azure AD подписываться на Azure AD B2C без настройки технического поставщика. Однако гостевые пользователи любого из этих тенантов **не смогут** войти в систему. Для этого необходимо будет [отдельно настроить каждый тенант](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> В следующих инструкциях клиент организации Azure AD будет называться "contoso.com", а клиент Azure AD B2C — "fabrikamb2c.onmicrosoft.com".

## <a name="prerequisites"></a>предварительным требованиям

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

А именно:
     
1. Создание клиента Azure Active Directory B2C (Azure AD B2C).
1. Создание приложения Azure AD B2C.    
1. Регистрация двух приложений подсистемы политик.  
1. Настройка ключей. 
1. Настройка начального пакета.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Шаг 1. Создание мультитенантного приложения Azure AD

Для предоставления возможности входа клиентам, использующим конечную точку мультитенантного поставщика Azure AD, необходимо мультитенантное приложение, зарегистрированное в любом из ваших тенантов Azure AD. В этой статье мы рассмотрим создание мультитенантного приложения Azure AD в клиенте Azure AD B2C. Затем разрешим вход для пользователей с помощью этого мультитенантного приложения Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com).
1. На верхней панели выберите учетную запись. В списке **Каталог** выберите клиента Azure AD B2C, в котором нужно зарегистрировать приложение Azure AD (fabrikamb2c.onmicrosoft.com).
1. В левой области щелкните **Больше служб** и выполните поиск по запросу "регистрация приложений".
1. Выберите **Регистрация нового приложения**.
1. Введите значение имя для приложения (например, `Azure AD B2C App`).
1. Выберите в качестве типа приложения **Веб-приложение или API**.
1. В поле **URL-адрес входа** введите следующий URL-адрес, где `yourtenant` заменяется на имя вашего клиента Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Значение для yourtenant в поле **URL-адрес входа** следует указывать в нижнем регистре.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Сохраните идентификатор приложения.
1. Щелкните созданное приложение.
1. В колонке **Параметры** выберите **Свойства**.
1. Задайте для параметра **Мультитенантный** значение **Да**.
1. В колонке **Параметры** щелкните **Ключи**.
1. Создайте ключ и сохраните его. Он понадобится для выполнения действий в следующем разделе.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Шаг 2. Добавление ключа Azure AD в Azure AD B2C

Зарегистрируйте ключ приложения в параметрах Azure AD B2C. Для этого:

1. Перейдите в меню параметров Azure AD B2C.
1. Последовательно выберите **Identity Experience Framework** > **Ключи политики**.
1. Щелкните **+Добавить**.
1. Выберите или введите следующие параметры:
   * Выберите **Вручную**.
   * Для параметра **Имя** выберите имя, соответствующее имени клиента Azure AD (например, `AADAppSecret`).  Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
   * Вставьте ключ приложения в текстовое поле **Секрет**.
   * Выберите **Подпись**.
1. Нажмите кнопку **Создать**.
1. Убедитесь, что вы создали ключ `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Шаг 3. Добавление поставщика утверждений в базовую политику

Чтобы пользователи выполняли вход с помощью Azure AD, необходимо определить Azure AD в качестве поставщика утверждений. Другими словами, необходимо указать конечную точку, с которой будет взаимодействовать Azure AD B2C. Конечная точка предоставит набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя. 

Можно определить Azure AD в качестве поставщика утверждений, добавив Azure AD в узел `<ClaimsProvider>` в файл расширения политики.

1. Откройте файл расширения (TrustFrameworkExtensions.xml) из рабочего каталога.
1. Найдите раздел `<ClaimsProviders>`. Если он не существует, добавьте его в корневой узел.
1. Добавьте новый `<ClaimsProvider>`, как показано ниже.

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

1. В узле `<ClaimsProvider>` укажите для `<Domain>` уникальное значение, позволяющее отличить этот поставщик удостоверений от других.
1. В узле `<TechnicalProfile>` обновите значение для `<DisplayName>`. Это значение будет отображаться на кнопке входа на экране входа в систему.
1. Обновите значение для `<Description>`.
1. Задайте для параметра `<Item Key="client_id">` идентификатор приложения из регистрации мультитенантного приложения Azure AD.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Шаг 3.1 ограничение доступа к определенным спискам тенантов Azure AD

> [!NOTE]
> Использование `https://sts.windows.net` в качестве значения для **ValidTokenIssuerPrefixes** позволит всем пользователям Azure AD войти в ваше приложение.

Вам необходимо обновить список допустимых издателей маркеров и ограничить доступ к определенному списку пользователей Azure AD, которые могут выполнить вход в систему. Чтобы получить значения, необходимо просмотреть метаданные для каждого клиента Azure AD, с помощью которого пользователи будут выполнять вход в систему. Данные имеют следующий формат: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, где `yourAzureADtenant` — имя вашего клиента Azure AD (contoso.com или любой другой клиент Azure AD).
1. Откройте веб-браузер и перейдите к URL-адресу метаданных.
1. Найдите на этой странице в браузере объект issuer и скопируйте его значение. Вы должны увидеть примерно следующее: `https://sts.windows.net/{tenantId}/`.
1. Вставьте значение для ключа `ValidTokenIssuerPrefixes`. Вы можете добавить несколько значений, разделив их запятой. Пример прокомментирован в образце XML выше.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Шаг 4. Регистрация поставщика утверждений для учетной записи Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Шаг 4.1. Создание копии пути взаимодействия пользователя

Теперь вам необходимо добавить поставщик удостоверений Azure AD в один из путей взаимодействия пользователя. На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа.

Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений Azure AD.

1. Откройте базовый файл политики (например, TrustFrameworkBase.xml).
1. Найдите элемент `<UserJourneys>` и скопируйте полный узел `<UserJourney>` с `Id="SignUpOrSignIn"`.
1. Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
1. Вставьте весь скопированный узел `<UserJourney>` как дочерний элемент `<UserJourneys>`.
1. Переименуйте идентификатор нового пути пользователя (например, переименуйте как `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Шаг 4.2. Отображение кнопки

Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если вы добавите для Azure AD элемент `<ClaimsProviderSelection>`, новая кнопка отобразится при переходе пользователя на страницу. Для этого:

1. Найдите узел `<OrchestrationStep>`, содержащий `Order="1"` в созданном пути пользователя.
1. Добавьте следующий код:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Задайте для параметра `TargetClaimsExchangeId` соответствующее значение. Мы советуем следовать общему соглашению: *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Шаг 4.3. Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Azure AD для получения токена. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений Azure AD.

1. Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
1. Добавьте следующий код:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Обновите `Id` до того же значения, которое имеет `TargetClaimsExchangeId` в предыдущем разделе.
1. Задайте для `TechnicalProfileReferenceId` значение идентификатора ранее созданного технического профиля (Common-AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Шаг 5. Создание новой политики проверяющей стороны

Теперь необходимо обновить файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.
 
1. Создайте копию SignUpOrSignIn.xml в рабочем каталоге и переименуйте его (например, на SignUpOrSignInWithAAD.xml).  
1. Откройте новый файл и задайте для атрибута `PolicyId` для `<TrustFrameworkPolicy>` новое уникальное значение (например, SignUpOrSignInWithAAD). Это будет имя вашей политики (например, B2C\_1A\_SignUpOrSignInWithAAD). 
1. Задайте для атрибута `ReferenceId` в `<DefaultUserJourney>` идентификатор созданного пути взаимодействия пользователя (например, SignUpOrSignUsingAzureAD). 
1. Сохраните изменения и отправьте файл. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Шаг 6. Отправка политики в клиент

1. На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и выберите **Azure AD B2C**.
1. Выберите **Инфраструктура процедур идентификации**.
1. Выберите **Все политики**.
1. Щелкните **Отправить политику**.
1. Установите флажок **Перезаписать политику, если она существует**.
1. Отправьте файл `TrustFrameworkExtensions.xml` и файл проверяющей стороны (например, `SignUpOrSignInWithAAD.xml`) и убедитесь, что они прошли проверку.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Шаг 7. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"

1. Откройте **параметры Azure AD B2C** и выберите **Identity Experience Framework**.
    > [!NOTE]
    > Для использования команды Run now (Запустить сейчас) необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

1. Откройте отправленную вами пользовательскую политику проверяющей стороны (*B2C\_1A\_SignUpOrSignInWithAAD*), а затем выберите **Run Now** (Запустить сейчас).
1. Теперь вы можете войти с использованием учетной записи Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>Шаг 8. Регистрация поставщика утверждений учетной записи Azure AD в пути взаимодействия пользователя "Изменение профиля" (необязательно)

Возможно, вы захотите добавить поставщик удостоверений учетной записи Azure AD в путь взаимодействия пользователя `ProfileEdit`. Чтобы сделать этот путь взаимодействия пользователя доступным, повторите шаги 4–6. Но на этот раз выберите узел `<UserJourney>`, который содержит `Id="ProfileEdit"`. Теперь сохраните, отправьте и протестируйте политику.

## <a name="troubleshooting"></a>Устранение неполадок

Для диагностики проблем ознакомьтесь со статьей по [устранению неполадок](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Дополнительная информация

Свои отзывы отправляйте сюда: [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
