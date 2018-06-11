---
title: Самостоятельное изменение пароля в Azure Active Directory B2C | Документация Майкрософт
description: В статье описано, как настроить самостоятельное изменение пароля для пользователей в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5474f469c6271a0c1348004664ead8b190de08c7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709144"
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C. Настройка изменения пароля с помощью настраиваемых политик  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

С помощью функции изменения пароля пользователи, выполнившие вход с использованием локальных учетных записей, могут изменять пароли. Для этого им не нужно подтверждать их подлинность путем проверки адреса электронной почты, как описано в статье о [последовательности самостоятельного сброса пароля](active-directory-b2c-reference-sspr.md). Если время сеанса истекает, когда пользователь входит в последовательность изменения пароля, такому пользователю предлагается выполнить вход еще раз. 

## <a name="prerequisites"></a>предварительным требованиям

Клиент Azure AD B2C, необходимый для регистрации или входа с использованием локальной учетной записи, как описано в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Как настроить изменение пароля в настраиваемых политиках

Чтобы настроить изменение пароля в настраиваемой политике, внесите следующие изменения в политику расширений инфраструктуры доверия. 

## <a name="define-a-claimtype-oldpassword"></a>Определение ClaimType oldPassword

Общая структура настраиваемой политики должна включать в себя `ClaimsSchema` и определять новый элемент `ClaimType` oldPassword, как показано ниже. 

```XML
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="oldPassword">
        <DisplayName>Old Password</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter password</UserHelpText>
        <UserInputType>Password</UserInputType>
      </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

Ниже представлены сведения о предназначении каждого элемента.

- Элемент `ClaimsSchema` определяет проверяющееся утверждение,  В этом случае будет проверяться старый пароль. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Добавление поставщика утверждений изменения пароля со вспомогательными элементами

Поставщик утверждений изменения пароля будет

1. Выполнять аутентификацию пользователя по старому паролю.
2. И если новый пароль соответствует значению подтверждения нового пароля, это значение будет сохранено в хранилище данных B2C, и пароль будет успешно изменен. 

![Рисунок](images/passwordchange.jpg)

Добавьте следующий поставщик утверждений в политику расширений. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="login-NonInteractive-PasswordChange">
          <DisplayName>Local Account SignIn</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
            <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
            <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
            <Item Key="ProviderName">https://sts.windows.net/</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">query</Item>
            <Item Key="scope">email openid</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
            <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
            <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
            <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
            <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Local Account Password Change</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
          <DisplayName>Change password (username)</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
            <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
```



### <a name="add-the-application-ids-to-your-custom-policy"></a>Добавление идентификаторов приложений в настраиваемую политику

Добавьте идентификаторы приложений в файл расширения (`TrustFrameworkExtensions.xml`):

1. В файле расширений (TrustFrameworkExtensions.xml) найдите элемент `<TechnicalProfile Id="login-NonInteractive">` и `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Замените все экземпляры `IdentityExperienceFrameworkAppId` идентификатором приложения Identity Experience Framework, как описано в [руководстве по началу работы](active-directory-b2c-get-started-custom.md). Вот пример: 

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Замените все экземпляры `ProxyIdentityExperienceFrameworkAppId` идентификатором прокси приложения Identity Experience Framework, как описано в [руководстве по началу работы](active-directory-b2c-get-started-custom.md).

4. Сохраните файл расширений.



## <a name="create-a-password-change-user-journey"></a>Создание пути взаимодействия пользователя для изменения пароля

```XML
 <UserJourneys>
    <UserJourney Id="PasswordChange">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

Вы внесли все необходимые изменения в файл расширения. Сохраните и отправьте этот файл. Убедитесь, что все проверки пройдены успешно.



## <a name="create-a-relying-party-rp-file"></a>Создание файла проверяющей стороны

Теперь обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию ProfileEdit.xml в рабочем каталоге. Затем переименуйте ее (например, PasswordChange.xml).
2. Откройте новый файл и задайте для атрибута `PolicyId` `<TrustFrameworkPolicy>` уникальное значение. Это имя вашей политики (например, PasswordChange).
3. Измените атрибут `ReferenceId` в `<DefaultUserJourney>` для обеспечения соответствия `Id` нового созданного пути взаимодействия пользователя (например, PasswordChange).
4. Сохраните изменения и отправьте файл.
5. Чтобы протестировать отправленную настраиваемую политику, на портале Azure перейдите к колонке политики и нажмите кнопку **Выполнить**.




## <a name="link-to-password-change-sample-policy"></a>Ссылка на образец политики изменения пароля

Образец политики можно найти [здесь](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 










