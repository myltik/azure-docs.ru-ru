---
title: Управление единым входом и настройкой токенов с помощью пользовательских политик | Документация Майкрософт
description: Управление единым входом и настройкой токенов с помощью пользовательских политик.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.openlocfilehash: c9eb7f7711a8987945b8aeaee8d6798b7a6b8284
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C. Управление SSO и настройкой токенов с помощью пользовательских политик
Использование пользовательских политик предоставляет тот же контроль над конфигурациями токена, сеанса и единого входа, что и при использовании встроенных политик.  Ознакомиться с функциями каждого параметра вы можете в [этом руководстве](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Конфигурация времени существования токенов и утверждений
Чтобы изменить настройки времени существования токенов, вам необходимо добавить элемент `<ClaimsProviders>` в файл проверяющей стороны политики, которую следует изменить.  Элемент `<ClaimsProviders>` является дочерним элементом `<TrustFrameworkPolicy>`.  Внутри необходимо разместить сведения, влияющие на время существования токенов.  Код XML будет выглядеть примерно так:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Время существования маркера доступа.** Время существования маркера доступа можно изменить, изменив значение внутри `<Item>`, используя Key="token_lifetime_secs" в качестве значения времени в секундах.  Значение по умолчанию во встроенных политиках составляет 3600 секунд (60 минут).

**Время существования токена идентификатора.** Время существования токена идентификатора можно изменить, изменив значение внутри `<Item>`, используя Key="token_lifetime_secs" в качестве значения времени в секундах.  Значение по умолчанию во встроенных политиках составляет 3600 секунд (60 минут).

**Время существования токена обновления.** Время существования токена обновления можно изменить, изменив значение внутри `<Item>`, используя Key="token_lifetime_secs" в качестве значения времени в секундах.  Значение по умолчанию во встроенных политиках составляет 1 209 600 секунд (14 дней).

**Время существования скользящего окна токена обновления.** Если вы хотите настроить время существования скользящего окна для токена обновления, измените значение внутри `<Item>`, используя Key="token_lifetime_secs" в качестве значения времени в секундах.  Значение по умолчанию во встроенных политиках составляет 7 776 000 секунд (90 дней).  Если вы не хотите задавать определенное время существования скользящего окна, замените эту строку следующей:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Утверждение издателя (iss).** Если вы хотите изменить утверждение издателя (iss), измените значение внутри `<Item>`, используя Key="IssuanceClaimPattern".  Применимые значения — `AuthorityAndTenantGuid` и `AuthorityWithTfp`.

**Утверждение настройки, представляющее идентификатор политики.** Параметры, использующиеся для настройки этого значения, — TFP (инфраструктура доверия) и ACR (ссылка на контекст проверки подлинности).  
Мы рекомендуем задать значение TFP. Для этого должны существовать `<Item>` с Key="AuthenticationContextReferenceClaimPattern", а установленное значение должно быть `None`.
Добавьте этот элемент в элемент `<OutputClaims>`:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Чтобы использовать ACR, удалите `<Item>` со значением Key="AuthenticationContextReferenceClaimPattern".

**Утверждение субъекта (sub).** По умолчанию для этого параметра задано значение ObjectID. Если вы хотите установить значение `Not Supported`, сделайте следующее:

Замените эту строку: 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
на следующую:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Режим работы сеанса и SSO
Чтобы изменить конфигурации режима работы сеанса и SSO, вам необходимо добавить элемент `<UserJourneyBehaviors>` в элемент `<RelyingParty>`.  Элемент `<UserJourneyBehaviors>` должен сразу же следовать за элементом `<DefaultUserJourney>`.  Содержимое элемента `<UserJourneyBehavors>` должно выглядеть следующим образом:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Конфигурация единого входа (SSO).** Чтобы изменить конфигурацию единого входа, вам необходимо изменить значение `<SingleSignOn>`.  Применимые значения — `Tenant`, `Application`, `Policy` и `Disabled`. 

**Время существования сеанса веб-приложения (в минутах).** Чтобы изменить время существования сеанса веб-приложения, вам необходимо изменить значение элемента `<SessionExpiryInSeconds>`.  Значение по умолчанию во встроенных политиках составляет 86 400 секунд (1440 минут).

**Время ожидания сеанса веб-приложения.** Чтобы изменить время ожидания сеанса веб-приложения, вам необходимо изменить значение `<SessionExpiryType>`.  Применимые значения — `Absolute` и `Rolling`.
