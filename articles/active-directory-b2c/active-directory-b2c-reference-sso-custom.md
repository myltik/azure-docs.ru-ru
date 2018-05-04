---
title: 'Azure AD B2C: управление сеансами единого входа с помощью настраиваемых политик | Документация Майкрософт'
description: Узнайте, как для управлять сеансами единого входа с помощью настраиваемых политик в Azure AD B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/20/2017
ms.author: davidmu
ms.openlocfilehash: ca7160d39d5d26ca69345ce636f22afbe44b25db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: управление сеансами единого входа

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C позволяет администратору управлять тем, как Azure AD B2C взаимодействует с пользователем после прохождения им аутентификации. Для этого используется управление сеансами единого входа. Например, администратор может управлять отображением выбранных поставщиков удостоверений или необходимостью повторного ввода данных локальной учетной записи. В этой статье описывается настройка параметров единого входа для Azure AD B2C.

## <a name="overview"></a>Обзор

Управление сеансами единого входа состоит из двух частей. Первая относится к непосредственному взаимодействию пользователя с Azure AD B2C, а вторая — к взаимодействию пользователя с внешними сторонами, например Facebook. Azure AD B2C не переопределяет и не обходит сеансы единого входа, которые могут поддерживаться внешними сторонами. Вместо этого маршрут для доступа к внешней стороне, проходящий через Azure AD B2C, "запоминается", устраняя необходимость повторно предлагать пользователю выбрать поставщик удостоверений социальных сетей или поставщик корпоративных удостоверений. Окончательный выбор сеанса единого входа остается за внешней стороной.

## <a name="how-does-it-work"></a>Как это работает?

Для управления сеансами единого входа используется та же семантика, что и для любого другого технического профиля в настраиваемых политиках. При выполнении шага оркестрации из технического профиля, связанного с шагом, запрашиваются ссылка на `UseTechnicalProfileForSessionManagement`. Затем, при наличии такового, указанный поставщик сеансов единого входа проверяется, чтобы определить, является ли пользователь участником сеанса. Если это так, то поставщик сеансов единого входа используется для повторного заполнения данных сеанса. Аналогичным образом после выполнения шага оркестрации поставщик используется для сохранения сведений в сеансе, если был указан поставщик сеансов единого входа.

В Azure AD B2C можно использовать определенное количество поставщиков сеансов единого входа.

* NoopSSOSessionProvider;
* DefaultSSOSessionProvider;
* ExternalLoginSSOSessionProvider;
* SamlSSOSessionProvider.

Классы управления единым входом указываются с помощью элемента `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` технического профиля.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Как можно понять из названия, этот поставщик не выполняет никаких действий. Его можно использовать для подавления реакции службы единого входа на события для конкретного технического профиля.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Этот поставщик можно использовать для хранения утверждений в сеансе. Как правило, этот поставщик указывается в техническом профиле, используемом для управления локальными учетными записями. 

> [!NOTE]
> При использовании DefaultSSOSessionProvider для хранения утверждений в рамках сеанса необходимо убедиться в следующем. Все утверждения, которые будут возвращены в приложение или использоваться в соответствии с предварительными условиями в последующих шагах, должны храниться в сеансе или считываться из из профиля пользователя в каталоге. Это убережет вас от сбоя аутентификации из-за отсутствующих утверждений.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Чтобы добавить утверждения в сеанс, используйте элемент `<PersistedClaims>` технического профиля. При использовании этого поставщика для повторного заполнения данных сеанса сохраненные утверждения добавляются в контейнер утверждений. `<OutputClaims>` используется для получения утверждений из сеанса.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Этот поставщик используется для подавления экрана "Выбор поставщика удостоверений". Обычно он указывается в техническом профиле, настроенном для внешнего поставщика удостоверений, например Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Этот поставщик используется для управления сеансами SAML в Azure AD B2C между приложениями, а также сеансами внешних поставщиков удостоверений SAML.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Технический профиль содержит два элемента метаданных.

| Элемент | По умолчанию | Возможные значения | ОПИСАНИЕ
| --- | --- | --- | --- |
| IncludeSessionIndex | Да | True или false | Указывает поставщику, что следует сохранить индекс сеанса. |
| RegisterServiceProviders | Да | True или false | Указывает, что поставщик должен зарегистрировать все поставщики услуг SAML, которыми было выдано утверждение. |

При использовании поставщика для хранения сеанса поставщика удостоверений SAML указанные выше элементы должны иметь значение false. При использовании поставщика для хранения сеанса SAML B2C указанные выше элементы должны иметь значение true или должны быть опущены, так как значением по умолчанию является true.

>[!NOTE]
> Для завершения выхода из сеанса SAML требуются `SessionIndex` и `NameID`.

## <a name="next-steps"></a>Дополнительная информация

Мы ценим отзывы и предложения. Если у вас возникли проблемы с этим, опубликуйте их на сайте Stack Overflow, используя тег [azure-ad-b2c](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Чтобы запросить какие-либо функции, можно проголосовать за них на нашем [форуме обратной связи](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

