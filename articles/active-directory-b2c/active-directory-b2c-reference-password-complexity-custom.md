---
title: Сложность пароля для настраиваемых политик Azure Active Directory B2C | Документация Майкрософт
description: Как настроить требования к сложности паролей в настраиваемой политике
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 9f8d576cbc5c2bb2fe4109086b04711422911390
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="configure-password-complexity-in-custom-policies"></a>Настройка сложности пароля в настраиваемых политиках

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье представлено расширенное описание понятия сложности пароля, а также сведения о том, как включить ее с помощью настраиваемых политик Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Настройка требований к сложности паролей в Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) поддерживает изменение требований сложности к паролям, которые пользователи указывают при создании учетной записи.  По умолчанию Azure AD B2C использует **надежные** пароли.  Azure AD B2C также поддерживает параметры конфигурации для управления сложностью паролей, используемых клиентами.  В этой статье рассказывается о настройке сложности пароля в настраиваемых политиках.  Вы также можете ознакомиться со статьей [Azure AD B2C: Настройка требований к сложности паролей](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>предварительным требованиям

Клиент Azure AD B2C, необходимый для регистрации или входа с использованием локальной учетной записи, как описано в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Как настроить сложность пароля в настраиваемых политиках

Чтобы настроить сложность пароля в настраиваемой политике, в общей структуре настраиваемой политики в элементе `BuildingBlocks` должны содержаться элементы `ClaimsSchema`, `Predicates` и `InputValidations`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Ниже представлены сведения о предназначении каждого элемента.

- Каждый элемент `Predicate` определяет основную проверку строки, при которой возвращается значение true или false.
- Элемент `InputValidations` содержит один или несколько элементов `InputValidation`.  Каждый элемент `InputValidation` создается с использованием ряда элементов `Predicate`. Этот элемент дает возможность выполнять логическое агрегирование (схожее с `and` и `or`).
- Элемент `ClaimsSchema` определяет проверяющееся утверждение,  а также определяет правило `InputValidation`, используемое для проверки утверждения.

### <a name="defining-a-predicate-element"></a>Определение элемента предиката

Предикаты предусматривают методы двух типов: IsLengthRange и MatchesRegex. Давайте рассмотрим пример каждого из них.  Сначала рассмотрим пример MatchesRegex, используемый для сопоставления регулярного выражения.  В этом примере он соответствует строке, содержащей числа.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Далее рассмотрим пример IsLengthRange.  Этот метод принимает минимальную и максимальную длину строки.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Используйте атрибут `HelpText` для предоставления пользователю сообщения об ошибке при неудачном завершении проверки.  Эту строку можно локализовать с помощью возможности [настройки языка](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Определение элемента InputValidation

Элемент `InputValidation` является агрегатом элемента `PredicateReferences`. Для успешного выполнения элемента `InputValidation` все элементы `PredicateReferences` должны иметь значение true.  Однако внутри элемента `PredicateReferences` используйте атрибут `MatchAtLeast`, чтобы указать количество проверок `PredicateReference` со значением true.  При необходимости определите атрибут `HelpText` для переопределения сообщения об ошибке, заданного в элементах `Predicate`, на которые он ссылается.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Определение элемента ClaimsSchema

Типы утверждений `newPassword` и `reenterPassword` считаются особыми, поэтому не следует изменять их имена.  Пользовательский интерфейс проверяет правильность повторно введенного пароля во время создания учетной записи на основе этих элементов `ClaimType`.  Такие же элементы `ClaimType` можно найти в файле TrustFrameworkBase.xml в начальном пакете.  В этом примере дополнительно показано переопределение этих элементов, чтобы определить `InputValidationReference`. Атрибут `ID` этого нового элемента указывает на определенный элемент `InputValidation`.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Сборка

В этом примере показано, как все части формируют рабочую политику.  Чтобы использовать этот пример, сделайте следующее:

1. Следуйте инструкциям в разделе "Предварительные требования" статьи [Azure Active Directory B2C: начало работы с настраиваемыми политиками](active-directory-b2c-get-started-custom.md), чтобы скачать, настроить и отправить TrustFrameworkBase.xml и TrustFrameworkExtensions.xml.
1. Создайте файл SignUporSignIn.xml, используя пример в этом разделе.
1. Обновите SignUporSignIn.xml, заменив `yourtenant` на свое имя клиента Azure AD B2C.
1. Отправьте файл политики SignUporSignIn.xml последним.

Данный пример содержит проверку для ПИН-кодов и надежных паролей:

- Поищите `PINpassword`. Элемент `InputValidation` проверяет ПИН-код любой длины.  Сейчас он не используется, так как он не указыван в элементе `InputValidationReference` внутри `ClaimType`. 
- Поищите `PasswordValidation`. Элемент `InputValidation` проверяет пароль от 8 до 16 знаков и содержит 3 из 4 знаков верхнего и нижнего регистра, цифры или символы.  Он указывается в `ClaimType`.  Таким образом, применение этого правила в этой политике обязательное.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">[a-z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">[A-Z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">[0-9]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">[!@#$%^*()]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
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
</TrustFrameworkPolicy>
```
