---
title: Azure Active Directory B2C. Добавление собственных атрибутов в пользовательские политики и их использование при изменении профиля | Документация Майкрософт
description: Пошаговое руководство по использованию свойств расширения и настраиваемых атрибутов, а также по их добавлению в пользовательский интерфейс
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
ms.openlocfilehash: e0595a67b90e1be7bb992ef7bda4343e692d8957
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C. Создание и использование настраиваемых атрибутов в пользовательской политике изменения профиля

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье вы создадите настраиваемый атрибут в каталоге Azure AD B2C и используете его в качестве пользовательского утверждения при изменении профиля.

## <a name="prerequisites"></a>предварительным требованиям

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Использование настраиваемых атрибутов для сбора данных о клиентах в Azure Active Directory B2C с помощью пользовательских политик
Каталог Azure Active Directory (Azure AD) B2C поставляется со встроенным набором атрибутов: Given Name, Surname, City, Postal Code, userPrincipalName и т. д.  Часто требуется создавать собственные атрибуты.  Например: 
* Клиентскому приложению требуется сохранить атрибут LoyaltyNumber.
* Поставщик удостоверений имеет уникальный идентификатор пользователя, который необходимо сохранить, например uniqueUserGUID.
* В настраиваемом пути взаимодействия пользователя нужно сохранить состояние, например migrationStatus.

С помощью Azure AD B2C можно расширить набор атрибутов, хранящихся в каждой учетной записи пользователя. Можно также читать и записывать эти атрибуты с помощью [API Graph Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

Свойства расширения расширяют схему объектов пользователей в каталоге.  В контексте этой статьи свойство расширения условий, настраиваемый атрибут и пользовательское утверждение подразумевают одно и то же, а имя изменяется в зависимости от контекста (приложение, объект, политика).

Свойства расширения можно зарегистрировать только в объекте приложения, даже если они содержат данные для пользователя. Свойство присоединяется к приложению. Объекту приложения нужно предоставить доступ на запись для регистрации свойства расширения. В один объект можно записать не более 100 значений расширений (для всех типов и приложений). Свойство добавляется к целевому каталогу и сразу становится доступным в клиенте каталога Azure AD B2C.
При удалении приложения эти свойства расширения и все данные, содержащиеся в них, для всех пользователей также удаляются. Если свойство расширения удаляется приложением, оно удаляется в объектах целевого каталога вместе со значениями.

Свойства расширения существуют только в контексте зарегистрированного приложения в клиенте. Идентификатор объекта приложения должен содержаться в техническом профиле, использующем его.

>[!NOTE]
>Каталог Azure AD B2C обычно включает веб-API с именем `b2c-extensions-app`.  Это приложение главным образом используется встроенными политиками B2C для пользовательских утверждений, созданных с помощью портала Azure.  Мы рекомендуем регистрировать расширения для пользовательских политик B2C с помощью этого приложения только для опытных пользователей.  Соответствующие инструкции содержатся в разделе "Дальнейшие действия" этой статьи.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Создание приложения для хранения свойств расширения

1. Откройте сеанс браузера, перейдите на [портал Azure](https://portal.azure.com) и выполните вход с использованием учетных данных администратора каталога B2C, который требуется настроить.
1. В области навигации слева щелкните **Active Directory**. Чтобы найти эту службу, щелкните > More Services (> Больше служб).
1. Щелкните **Регистрация приложений** и выберите пункт **Регистрация нового приложения**.
1. Укажите следующие рекомендуемые записи:
  * Укажите имя для веб-приложения: **WebApp-GraphAPI-DirectoryExtensions**
  * Тип приложения: веб-приложение или API.
  * URL-адрес входа: https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions.
1. Выберите "Создать". В области **уведомлений** появится уведомление об успешном выполнении.
1. Выберите созданное только что веб-приложение: **WebApp-GraphAPI-DirectoryExtensions**
1. Выберите параметры: **Необходимые разрешения**
1. Выберите API: **Microsoft Azure Active Directory**.
1. Установите флажки в разделе "Разрешения приложения": **Чтение и запись данных каталога** и **Сохранение**
1. Выберите команду **Предоставить разрешения** и нажмите кнопку **Да**.
1. Скопируйте в буфер обмена и сохраните следующие идентификаторы из раздела "WebApp-GraphAPI-DirectoryExtensions > Параметры > Свойства".
*  **Идентификатор приложения**. Пример: `103ee0e6-f92d-4183-b576-8c3739027780`
* **Идентификатор объекта**. Пример: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Изменение пользовательской политики для добавления ApplicationObjectId

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
><TechnicalProfile Id="AAD-Common"> называется общим, так как его элементы содержатся и повторно используются во всех технических профилях Azure Active Directory с элементом `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`.

>[!NOTE]
>При первой записи технического профиля в созданное свойство расширения может произойти одноразовая ошибка.  Свойство расширения создается при первом использовании.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Использование нового свойства расширения или настраиваемого атрибута в пути взаимодействия пользователя


1. Откройте файл проверяющей стороны, в котором представлено описание пути взаимодействия пользователя с политикой изменения.  Сначала мы рекомендуем скачать уже настроенную версию файла RP-PolicyEdit непосредственно из раздела пользовательской политики Azure B2C на портале Azure.  Вы также можете открыть XML-файл из папки хранилища.
2. Добавьте пользовательское утверждение `loyaltyId`.  После добавления пользовательского утверждения в элемент `<RelyingParty>` оно будет передано как параметр в UserJourney TechnicalProfiles и включено в токен для приложения.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Добавьте определение утверждений в файл политики расширения `TrustFrameworkExtensions.xml` в элементе `<ClaimsSchema>`, как показано ниже.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Добавьте это же определение утверждения в базовый файл политики `TrustFrameworkBase.xml`.  
>Обычно не нужно добавлять определение `ClaimType` в базовый файл и файл расширения. Но так как на следующих шагах параметр extension_loyaltyId будет добавлен в TechnicalProfiles в базовом файле, проверяющий политики отклонит отправку базового файла без этого определения.
>Отследите выполнение пути взаимодействия пользователя ProfileEdit в файле TrustFrameworkBase.xml.  Найдите путь взаимодействия пользователя с тем же именем в редакторе и обратите внимание, что на шаге 5 оркестрации вызывается TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Найдите и проверьте этот TechnicalProfile, чтобы ознакомиться с рабочим процессом.
5. Добавьте loyaltyId в качестве входного и выходного утверждения в техническом профиле SelfAsserted-ProfileUpdate.
```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Добавьте утверждение в техническом профиле AAD-UserWriteProfileUsingObjectId, чтобы сохранить значение утверждения в свойство расширения для текущего пользователя в каталоге.
```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Добавьте утверждение в технический профиль AAD-UserReadUsingObjectId, чтобы считывать значение атрибута расширения при каждом входе пользователя в систему. До сих пор TechnicalProfiles изменялся только при работе с локальными учетными записями.  Если новый атрибут нужно использовать для учетной записи социальной сети или федеративной учетной записи, нужно изменить другой набор технических профилей. Ознакомьтесь с разделом "Дальнейшие действия".

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>Элемент IncludeTechnicalProfile добавляет все элементы AAD-Common в этот технический профиль.

## <a name="test-the-custom-policy-using-run-now"></a>Тестирование настраиваемой политики с помощью параметра "Запустить сейчас"
1. Откройте колонку **Azure AD B2C** и последовательно выберите **Identity Experience Framework > Настраиваемые политики**.
1. Выберите отправленную вами настраиваемую политику и нажмите кнопку **Запустить сейчас**.
1. Вы сможете зарегистрироваться, используя адрес электронной почты.

Идентификатор токена, отправляемый в приложение, содержит новое свойство расширения в виде пользовательского утверждения, перед которым будет указано extension_loyaltyId. Ознакомьтесь с примером ниже.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Дополнительная информация

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Добавьте новое утверждение в рабочий процесс входа с использованием учетных записей социальных сетей, изменив технические профили, указанные ниже. Эти два технических профиля используются при входе с использованием учетной записи социальной сети или федеративной учетной записи для записи и чтения данных пользователя с применением alternativeSecurityId в качестве указателя объекта пользователя.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Использование одинаковых атрибутов расширения во встроенных и пользовательских политиках.
При добавлении атрибутов расширения (пользовательских атрибутов) на портале они регистрируются с помощью приложения b2c-extensions-app, содержащегося в каждом клиенте B2C.  Чтобы использовать эти атрибуты расширения в пользовательской политике, сделайте следующее:
1. В клиенте B2C на сайте portal.azure.com перейдите к **Azure Active Directory** и выберите **Регистрация приложений**
2. Найдите свое приложение **b2c-extensions-app** и выберите его.
3. В разделе "Основные компоненты" запишите **идентификатор приложения** и **идентификатор объекта**.
4. Добавьте их в метаданные технического профиля AAD-Common следующим образом:

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

Чтобы обеспечить согласованность с интерфейсом портала, создайте эти атрибуты с помощью пользовательского интерфейса портала *прежде чем* использовать их в пользовательских политиках.  При создании атрибута ActivationStatus на портале необходимо обратиться к нему следующим образом:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Справочные материалы

* **Технический профиль** — это элемент, который можно считать *функцией*, определяющей имя конечной точки, ее метаданные, протокол, а также предоставляющей сведения об обмене утверждениями, который должна выполнять инфраструктура процедур идентификации.  При вызове этой *функции* на шаге оркестрации или из другого технического профиля вызывающий объект указывает InputClaims и OutputClaims в качестве параметров.


* Полный список свойств расширения см. в статье [РАСШИРЕНИЯ СХЕМЫ КАТАЛОГОВ | ОБЩИЕ ПОНЯТИЯ API GRAPH](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

>[!NOTE]
>Атрибуты расширений в API Graph именуются по соглашению `extension_ApplicationObjectID_attributename`. В пользовательских политиках атрибуты расширений указываются в формате extension_attributename, тем самым исключая ApplicationObjectId в XML.
