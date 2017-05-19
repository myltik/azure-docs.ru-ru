---
title: "Пошаговое руководство. Обмен утверждениями REST API как один из необходимых этапов в пользовательских политиках B2C | Документация Майкрософт"
description: "В этой статье описывается интеграция пользовательских политик Azure Active Directory B2C с API"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 27d066cc6f985565a765989837e4a2744387a628
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-an-orchestration-step"></a>Пошаговое руководство по интеграции обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как этапа оркестрации

**Инфраструктура процедур идентификации**, лежащая в основе Azure AD B2C, позволяет разработчикам служб удостоверений интегрировать взаимодействие с REST API в пути взаимодействия пользователя.  

В конце этого пошагового руководства вы сможете создавать пути взаимодействия пользователей Azure AD B2C, которые взаимодействуют со службами RESTful.

Инфраструктура процедур идентификации отправляет и получает данные в утверждениях.  Обмен утверждениями REST API может разрабатываться как этап оркестрации.

- Этот обмен может вызывать внешнее действие, например во внешнюю базу данных может записываться событие.
- Этот процесс может также использоваться, чтобы получить значение и сохранить его в базе данных пользователя.
- Полученные утверждения могут позже использоваться для изменения процедуры выполнения.

Взаимодействие можно также реализовать как профиль проверки. Дополнительные сведения см. в [пошаговом руководстве по интеграции обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверки входных данных](active-directory-b2c-rest-api-validation-custom.md).

В сценарии выполняется поиск пользователя (изменяющего профиль) во внешней системе, получение места регистрации пользователя и возвращение этого атрибута как утверждения обратно в приложение.

## <a name="prerequisites"></a>Предварительные требования

- Клиент Azure AD B2C, необходимый для регистрации или входа с использованием локальной учетной записи, как описано в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).
- Конечная точка REST API для взаимодействия. В этом пошаговом руководстве в качестве примера используется очень простой элемент webhook приложений-функций Azure.
- **Мы рекомендуем** ознакомиться с [пошаговым руководством по использованию обмена утверждениями REST API как проверки](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1---prepare-the-rest-api-function"></a>Шаг 1. Подготовка функции REST API

> [!NOTE]
> В рамках этой статьи настройка функций REST API не рассматривается. [Приложения-функции Azure](https://docs.microsoft.com/azure/azure-functions/functions-reference) предоставляют отличный набор средств для создания служб RESTful в облаке.

Мы настроили функцию Azure, которая получает утверждение `email` и возвращает утверждение `city` с присвоенным значением `Redmond`. Пример функции Azure можно найти здесь: [Github](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

Утверждение `userMessage`, возвращенное функцией Azure, является необязательным в этом контексте и будет игнорироваться инфраструктурой процедур идентификации.  Потенциально его можно использовать в качестве сообщения, переданного приложению, а затем позже представленного пользователю.

```
if (requestContentAsJObject.email == null)
    {
        return request.CreateResponse(HttpStatusCode.BadRequest);
    }

    var email = ((string) requestContentAsJObject.email).ToLower();


     return request.CreateResponse<ResponseContent>(
            HttpStatusCode.OK,
            new ResponseContent
            {
                version = "1.0.0",
                status = (int) HttpStatusCode.OK,
                userMessage = "User Found",
                city = "Redmond"
            },
            new JsonMediaTypeFormatter(),
            "application/json");
```

С помощью **приложений-функций Azure** можно легко получить URL-адрес функции, который включает идентификатор определенной функции.  В этом случае URL-адрес — https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Вы можете его использовать также для тестирования.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Шаг 2. Настройка обмена утверждениями RESTful API как технического профиля в файле TrustFrameworkExtensions.xml

Технический профиль представляет собой полную конфигурацию обмена, заданную с помощью службы RESTful. Откройте файл `TrustFrameworkExtensions.xml` и добавьте приведенный ниже фрагмент XML-кода в элемент `<ClaimsProvider>`.

> [!NOTE]
> Мы советуем использовать поставщик RESTful версии 1.0.0.0, описанный ниже, как протокол и функцию, которая будет взаимодействовать с внешней службой.  Полное определение схемы см. здесь: <!-- TODO: Link to RESTful Provider schema definition>-->.

```XML
<ClaimsProvider>
        <DisplayName>REST APIs</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">     
                <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <Metadata>
                    <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                    <Item Key="AuthenticationType">None</Item>
                    <Item Key="SendClaimsIn">Body</Item>
                </Metadata>
                <InputClaims>
                    <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
                </InputClaims>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
                </OutputClaims>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
```

Элемент `<InputClaims>` определяет утверждения, которые отправляются из инфраструктуры процедур идентификации в службу REST. В примере выше содержимое утверждения `givenName` отправляется в службу REST как утверждение `email`.  

Элемент `<OutputClaims>` определяет утверждения, которые инфраструктура процедур идентификации ожидает получить из службы REST. Несмотря на количество полученных утверждений, инфраструктура процедур идентификации будет использовать только те, которые указаны здесь. В этом примере утверждение, полученное как `city`, будет сопоставлено с утверждением инфраструктуры процедур идентификации `city`.

## <a name="step-3---add-a-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Шаг 3. Добавление нового утверждения `city` в схему файла TrustFrameworkExtensions.xml

Утверждение `city` указано в схеме строго определенным образом. Поэтому мы добавим определение внутрь элемента `<BuildingBlocks>`, который можно найти в начале файла `TrustFrameworkExtensions.xml`.

```XML
<BuildingBlocks>
<!--The claimtype city must be added to the TrustFrameworkPolicy-->
<!-- You can add new claims in the BASE file Section III, or in the extensions file-->
<ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

## <a name="step-4---include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-your-trustframeworkextensionsxml"></a>Шаг 4. Включение обмена утверждениями службы REST как этапа оркестрации при изменении профиля в файле TrustFrameworkExtensions.xml

Мы решили добавить такой этап как изменение профиля после проверки подлинности пользователя (этапы оркестрации 1–4 см. ниже) и предоставления обновленных сведений о профиле (шаг 5).

> [!NOTE]
> Вызов REST API может использоваться как этап оркестрации в разных ситуациях.  Как этап оркестрации этот вызов может использоваться в качестве обновления внешней системы после успешного выполнения пользователем задания, например первой регистрации или обновления профиля для синхронизации сведений.  В этом случае вызов REST API используется для расширения сведений, предоставленных приложению после изменения профиля.

Скопируйте XML-код пути взаимодействия пользователя для изменения профиля из файла `TrustFrameworkBase.xml` в файл `TrustFrameworkExtensions.xml` внутри элемента `<UserJourneys>`, а затем внесите необходимые изменения, описанные на шаге 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
          <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
```

> [!IMPORTANT]
> Если порядок не соответствует вашей версии, вставьте как шаг выше строку для указания типа ClaimsExchange `SendClaims`.

Конечный XML-код UserJourney должен выглядеть следующим образом:

```XML
<UserJourney Id="ProfileEdit">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>localAccountAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>socialIdpAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
          </ClaimsExchanges>
           </OrchestrationStep>
           <!-- Add a step 6 to the user journey before the jwt token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
          <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
            </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
```

## <a name="step-5---add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Шаг 5. Добавление утверждения city в файл политики проверяющей стороны для отправки утверждения приложению

Чтобы выполнить это, необходимо изменить файл проверяющей стороны `ProfileEdit.xml`, а также элемент `<TechnicalProfile Id="PolicyProfile">` для добавления `<OutputClaim ClaimTypeReferenceId="city" />`.

После добавления нового утверждения технический профиль должен выглядеть следующим образом:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6---upload-your-changes-and-test"></a>Шаг 6. Передача изменений и проверка

Вам потребуется перезаписать существующие версии политики.

1.    (Необязательно.) Перед продолжением вы можете сохранить существующую версию файла расширений (скачав ее).  Мы не рекомендуем передавать несколько версий файла расширений, чтобы не усложнять работу.
2.    (Необязательно.) Вы можете переименовать новую версию файла политики изменения PolicyId, изменив PolicyId="B2C_1A_TrustFrameworkProfileEdit".
3.    Отправьте файл расширений.
4.    Отправьте файл проверяющей стороны для изменения политики.
5.    Используйте параметр **Запустить сейчас**, чтобы проверить политику.  Просмотрите маркер, возвращенный инфраструктурой процедур идентификации в приложение.

Если все установлено правильно, токен будет содержать новое утверждение `city` со значением `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Дальнейшие действия

[Walkthrough: Integrate REST API claims exchanges in your Azure AD B2C user journeys as validation on user input](active-directory-b2c-rest-api-validation-custom.md) (Пошаговое руководство. Интеграция обмена утверждениями REST API в путях взаимодействия пользователей Azure AD B2C как проверка входных данных)

[Azure Active Directory B2C: Creating and using custom attributes in a custom profile edit policy.](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) (Azure Active Directory B2C. Создание и использование настраиваемых атрибутов в пользовательской политике изменения профиля)

