---
title: Azure Active Directory B2C. Настройка языка в настраиваемых политиках | Документация Майкрософт
description: Узнайте, как использовать локализованное содержимое в пользовательских политиках для нескольких языков
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 11/13/2017
ms.author: davidmu
ms.openlocfilehash: 45cfa152615da1447cc695e0dd201e5b8d046cf4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="language-customization-in-custom-policies"></a>Настройка языка в пользовательских политиках

> [!NOTE]
> Эта функция предоставляется в общедоступной предварительной версии.
> 

В пользовательских политиках настройка языка работает так же, как и во встроенных политиках.  См. встроенную [документацию](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization), в которой описано поведение системы при выборе языка на основе параметров и настроек браузера.

## <a name="enable-supported-languages"></a>Включение поддерживаемых языков
Если языковые стандарты пользовательского интерфейса не указаны и браузер пользователя запрашивает один из этих языков, пользователю отображаются поддерживаемые языки.  

Поддерживаемые языки определяются в коде `<BuildingBlocks>` в следующем формате:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

Язык по умолчанию и поддерживаемые языки ведут себя так же, как и во встроенных политиках.

## <a name="enable-custom-language-strings"></a>Включение строк пользовательского языка

Для создания строк пользовательского языка следует выполнить два шага:
1. Изменить `<ContentDefinition>` для страницы, чтобы указать идентификатор ресурса для нужных языков.
2. Создать раздел `<LocalizedResources>` с соответствующими идентификаторами в `<BuildingBlocks>`.

Следует помнить, что `<ContentDefinition>` и `<BuildingBlock>` можно разместить как в файле расширения, так и в файле исходной политики в зависимости от того, нужны ли эти изменения во всех наследуемых политиках или нет.

### <a name="edit-the-contentdefinition-for-the-page"></a>Изменение ContentDefinition для страницы

Для каждой страницы, которую необходимо локализовать, можно указать в `<ContentDefinition>`, какие языковые ресурсы следует выбирать для каждого кода языка.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

В этом примере к унифицированной странице регистрации или входа добавляются пользовательские строки на французском (fr) и английском (en) языках.  `LocalizedResourcesReferenceId` для каждого `LocalizedResourcesReference` совпадает с языковым стандартом, но в качестве идентификатора можно использовать любую строку.  Для каждого сочетания языка и страницы необходимо создать соответствующий код `<LocalizedResources>`, как показано далее.


### <a name="create-the-localizedresources"></a>Создание LocalizedResources

Переопределения содержатся в `<BuildingBlocks>`, и существует `<LocalizedResources>` для каждой страницы и языка, указанного в `<ContentDefinition>` для каждой страницы.  Каждое переопределение задается как `<LocalizedString>`, как показано в следующем примере:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Существует четыре типа строковых элементов на странице:

**ClaimsProvider** — метки для поставщиков удостоверений (Facebook, Google, Azure AD и т. д.). **ClaimType** — метки для атрибутов и соответствующего текста справки или ошибок проверки полей. **UxElement** — другие строки элементов на странице, которые присутствуют по умолчанию, например кнопки, ссылки или текст. **ErrorMessage** — сообщения об ошибках проверки форм.

Убедитесь, что значения `StringId` соответствуют странице, используемой в этих переопределениях. Иначе она будет заблокирована проверкой политики при отправке.  