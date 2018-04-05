---
title: Отслеживание поведения пользователей с помощью событий в Application Insights из Azure AD B2C | Документация Майкрософт
description: Пошаговое руководство по включению журналов событий в Application Insights из путей взаимодействия пользователей Azure AD B2C с использованием настраиваемых политик (предварительная версия)
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Отслеживание поведения пользователей в путях взаимодействия Azure AD B2C с использованием Application Insights

Azure Active Directory B2C можно использовать с Azure Application Insights.  Они предоставляют подробные настраиваемые журналы событий для настраиваемых путей взаимодействия пользователя.  В этом руководстве показано, как выполнить следующее: 
* Получить сведения о поведении пользователей.
* Устранить проблемы в собственных политиках в среде разработки или в рабочей среде.
* Измерить производительность.
* Создать уведомления из Application Insights.

## <a name="how-it-works"></a>Принцип работы
В Identity Experience Azure AD B2C добавлен новый поставщик: `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Он отправляет данные о событиях непосредственно в Application Insights с использованием ключа инструментирования, предоставляемого в Azure AD B2C.  Технический профиль использует этот поставщик для определения события из B2C.  Профиль определяет имя события, утверждения, которые будут записаны, и ключ инструментирования.  Для публикации события технический профиль добавляется как `orchestration step` или `validation technical profile` в настраиваемом пути взаимодействия пользователя.  События могут быть унифицированы в Application Insights с использованием идентификатора корреляции для записи сеанса пользователя.  Application Insights делает событие и сеанс доступными в течение нескольких секунд и предоставляет множество инструментов визуализации, экспорта и аналитики.



## <a name="prerequisites"></a>предварительным требованиям
Выполните шаги, описанные в статье [Azure Active Directory B2C: начало работы с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).  При работе с этим руководством предполагается, что используется начальный пакет настраиваемой политики.  Однако это необязательно.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Шаг 1. Создайте ресурс Application Insights и получите ключ инструментирования.

Application Insights является мощным средством. При его использовании с Azure AD B2C единственным требованием является создание ресурса и получение ключа инструментирования.  Application Insights следует создать на [портале Azure](https://portal.azure.com).

[Документация по Application Insights](https://docs.microsoft.com/azure/application-insights/)

1. Щелкните `+ Create a resource` на портале Azure в вашем клиенте для подписки.  Этот клиент не является вашим клиентом Azure AD B2C.  
2. Найдите и выберите `Application Insights`.  
3. Создайте ресурс `Application Type` `ASP.NET web application` в рамках предпочитаемой подписки.
4. После этого откройте ресурс Application Insights и обратите внимание на `Instrumentation Key`. 

![Обзор Application Insights и ключ инструментирования](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Шаг 2. Добавление новых определений ClaimType в файл расширения инфраструктуры доверия

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>Откройте файл расширения из начального пакета и добавьте следующие элементы в узел `<BuildingBlocks>`.  Файл расширения обычно имеет имя `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

```xml

    <ClaimsSchema>
      <ClaimType Id="EventType">
        <DisplayName>EventType</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="PolicyId">
        <DisplayName>PolicyId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="Culture">
        <DisplayName>Culture</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="CorrelationId">
        <DisplayName>CorrelationId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <!--Additional claims used for passing claims to Application Insights Provider -->
      <ClaimType Id="federatedUser">
        <DisplayName>federatedUser</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="parsedDomain">
        <DisplayName>Parsed Domain</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>The domain portion of the email address.</UserHelpText>
      </ClaimType>
      <ClaimType Id="userInLocalDirectory">
        <DisplayName>userInLocalDirectory</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
    </ClaimsSchema>

```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Шаг 3. Добавление новых технических профилей, использующих поставщик Application Insights

Технические профили можно использовать как функции Identity Experience Framework Azure AD B2C.  В этом примере определены пять технических профилей, чтобы открыть сеанс и опубликовать события.

| Технический профиль       | Задача |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | Создает общий набор параметров, который будет включен во все технические профили Azure-Insights.     | 
| JourneyContextForInsights   | Открывает сеанс в Application Insights и отправляет идентификатор корреляции. |
| AzureInsights-SignInRequest     | Создает событие SignIn с набором требований при получении запроса на вход.      | 
| AzureInsights-UserSignup | Создает событие под названием UserSignup, когда пользователь запускает настраиваемый параметр регистрации в пути взаимодействия регистрации или входа.     | 
| AzureInsights-SignInComplete | Записывает успешное выполнение аутентификации, когда токен передается в приложение проверяющей стороны.     | 

Добавьте профили в файл расширения из начального пакета путем добавления этих элементов в узел `<ClaimsProviders>`.  Файл расширения обычно имеет имя `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`.

> [!IMPORTANT]
> Замените `Instrumentation Key` в техническом профиле `ApplicationInsights-Common` идентификатором GUID, предоставленным ресурсом Application Insights.

```xml
<ClaimsProvider>
      <DisplayName>Application Insights</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="JourneyContextForInsights">
          <DisplayName>Application Insights</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="CorrelationId" />
          </OutputClaims>
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInRequest">
          <InputClaims>
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInComplete">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
            <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
            <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-UserSignup">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-Common">
          <DisplayName>Alternate Email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
            <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Шаг 4. Добавьте технические профили для Application-Insights в качестве шагов оркестрации в имеющемся пути взаимодействия пользователя.

Вызовите `JournyeContextForInsights` в качестве первого шага оркестрации.

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Вызовите `Azure-Insights-SignInRequest` в качестве второго шага оркестрации для отслеживания получения запроса на вход или регистрацию.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Непосредственно **перед** шагом оркестрации `SendClaims` добавьте новый шаг, который вызывает `Azure-Insights-UserSignup`. Он запускается, когда кнопка регистрации нажимается в процессе регистрации или входа.

```xml
        <!-- Handles the user clicking the sign up link in the local account sign in page -->
        <OrchestrationStep Order="9" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newUser</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>newUser</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
          </ClaimsExchanges>
```

Сразу после шага регистрации `SendClaims` вызовите `Azure-Insights-SignInComplete`.   Этот шаг будет означать успешное завершение пути взаимодействия.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> После добавления новых шагов оркестрации повторно последовательно пронумеруйте шаги, не пропуская ни одного целого числа от 1 до N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Шаг 5. Отправьте измененный файл расширений, запустите политику и просмотрите события в Application Insights.

Сохраните и отправьте новый файл расширений инфраструктуры доверия.  Затем вызовите политику проверяющей стороны из своего приложения или используйте `Run Now` в интерфейсе Azure AD B2C.  Через несколько секунд события будут доступны в Application Insights.

1. Откройте ресурс Application Insights в своем клиенте Azure Active Directory.
2. Щелкните `Events` в подменю `USAGE`.
3. Задайте для `During` значение `Last hour` и для `By` — `3 minutes`.  Чтобы просмотреть результаты, щелкните `Refresh`.

![Использование Application Insights. Колонка "События".](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>СЛЕДУЮЩИЕ ШАГИ

Добавьте дополнительные типы утверждений и события в путь взаимодействия пользователя в соответствии со своими потребностями.  Ниже приведен список возможных утверждений с использованием дополнительных сопоставителей утверждений.

### <a name="culture-specific-claims"></a>Утверждения, которые содержат сведения о языке

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Утверждения, которые содержат сведения о политике

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>Утверждения, которые содержат сведения об OpenIDConnect

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Не протокольные параметры, включенные в запросы OIDC и OAuth2

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Любое имя параметра, включенное в запрос OIDC или OAuth2, можно сопоставить с утверждением в пути взаимодействия пользователя.  Затем его можно записать в событие. Например, запрос из приложения может включать в себя параметр строки запроса с именем `app_session`, `loyalty_number` или `any_string`.

Пример запроса из приложения:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Затем утверждения можно добавить, добавив элемент входящего утверждения в событие Application Insights с использованием такого кода:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Другие системные утверждения

Некоторые системные утверждения следует добавить в контейнер утверждений, прежде чем они будут доступны для записи в качестве событий. Технический профиль `SimpleUJContext` должен вызываться как шаг оркестрации или технический профиль проверки, прежде чем эти утверждения будут доступны.

```xml
<ClaimsProvider>
    <DisplayName>User Journey Context Provider</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="SimpleUJContext">
                <DisplayName>User Journey Context Provide</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="IP-Address" />
                    <OutputClaim ClaimTypeReferenceId="CorrelationId" />
                    <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
                    <OutputClaim ClaimTypeReferenceId="Build" />
                 </OutputClaims>
            </TechnicalProfile>
        </TechnicalProfiles>
</ClaimsProvider>



