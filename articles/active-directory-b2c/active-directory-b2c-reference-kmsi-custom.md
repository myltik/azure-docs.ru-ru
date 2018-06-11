---
title: Оставаться в системе в Azure Active Directory B2C | Документация Майкрософт
description: Статья, в которой описано, как настроить функцию "Оставаться в системе".
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a32aaa5d91426199c29765d2d9645e8a4ddb03b4
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709161"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: включение функции "Оставаться в системе"  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В Azure AD B2C теперь можно включать функцию "Оставаться в системе" в веб-приложениях и собственных приложениях. Эта функция предоставляет пользователям, которые возвращаются в приложение, доступ без запроса на повторный ввод имени пользователя и пароля. Доступ отменяется, когда пользователь выходит из системы. 

Мы не рекомендуем пользователям включать эту функцию на общедоступных компьютерах. 

![Рисунок](images/kmsi.PNG)


## <a name="prerequisites"></a>предварительным требованиям

Клиент Azure AD B2C, настроенный для разрешения регистрации или входа с локальной учетной записью, как описано в [руководстве по началу работы](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Включение функции "Оставаться в системе"

Внесите следующие изменения в политику расширений инфраструктуры доверия.

## <a name="adding-a-content-definition-element"></a>Добавление элемента определения содержимого 

Узел `BuildingBlocks` файла расширения должен содержать элемент `ContentDefinitions`. 

1. В разделе `ContentDefinitions` определите новый элемент `ContentDefinition` с идентификатором `api.signuporsigninwithkmsi`.
2. Новый элемент `ContentDefinition` должен содержать `LoadUri`, `RecoveryUri` и `DataUri`, как показано ниже.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` — это машиночитаемый идентификатор, который используется для отображения флажка "Оставаться в системе" на страницах входа. Ни в коем случае не изменяйте это значение. 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>Добавление поставщика утверждений входа локальной учетной записи 

Определить локальную учетную запись (Local Account SignIn) в качестве поставщика утверждений можно в узле `<ClaimsProvider>` файла расширения политики:

1. Откройте файл расширения (TrustFrameworkExtensions.xml) из рабочего каталога. 
2. Найдите раздел `<ClaimsProviders>`. Если он не существует, добавьте его в корневой узел.
3. Начальный пакет из [руководства по началу работы](active-directory-b2c-get-started-custom.md) содержит поставщик утверждений Local Account SignIn. 
4. Если он отсутствует, добавьте новый узел `<ClaimsProvider>`, как показано ниже.

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
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

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Создание функции "Оставаться в системе" во включенном пути взаимодействия пользователя

Теперь необходимо добавить поставщик утверждений Local Account SignIn в путь взаимодействия пользователя. 

1. Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2. Найдите элемент `<UserJourneys>` и скопируйте полный узел `<UserJourney>` с `Id="SignUpOrSignIn"`.
3. Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
4. Вставьте весь скопированный узел `<UserJourney>` как дочерний элемент `<UserJourneys>`.
5. Переименуйте идентификатор нового пути пользователя (например, переименуйте как `SignUpOrSignInWithKmsi`).
6. Наконец, в `OrchestrationStep 1` измените `ContentDefinitionReferenceId` на `api.signuporsigninwithkmsi`, как было определено на предыдущих шагах. Это необходимо, чтобы включить флажок в пути взаимодействия пользователя. 
7. Вы внесли все необходимые изменения в файл расширения. Сохраните и отправьте этот файл. Убедитесь, что все проверки пройдены успешно.

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>Создание файла проверяющей стороны

Теперь обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию SignUpOrSignIn.xml в рабочем каталоге. Затем переименуйте ее (например, SignUpOrSignInWithKmsi.xml).

2. Откройте новый файл и задайте для атрибута `PolicyId` `<TrustFrameworkPolicy>` уникальное значение. Это имя вашей политики (например, SignUpOrSignInWithKmsi).

3. Измените атрибут `ReferenceId` в `<DefaultUserJourney>` для сопоставления `Id` нового созданного пути взаимодействия пользователя (например, SignUpOrSignInWithKmsi).

4. Функция "Оставаться в системе" будет настроена в `UserJourneyBehaviors`. 

5. Параметр **`KeepAliveInDays`** контролирует, как долго пользователь будет оставаться в системе. В следующем примере сеанс функции "Оставаться в системе" автоматически заканчивается через 14 дней, независимо от того, как часто пользователь выполняет автоматическую проверку подлинности.

   Если задать для параметра `KeepAliveInDays` значение 0, функция "Оставаться в системе" будет отключена. По умолчанию это значение равно 0.

6. Если **`SessionExpiryType`** имеет значение *Rolling*, то сеанс функции "Оставаться в системе" продлевается на 14 дней каждый раз, когда пользователь выполняет автоматическую проверку подлинности.  Если выбрано значение *Rolling*, рекомендуется поддерживать минимальное количество дней. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. Сохраните изменения и отправьте файл.

8. Чтобы протестировать отправленную настраиваемую политику, на портале Azure перейдите к колонке политики и нажмите кнопку **Выполнить**.


## <a name="link-to-sample-policy"></a>Ссылка на пример политики

Пример политики можно найти [здесь](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








