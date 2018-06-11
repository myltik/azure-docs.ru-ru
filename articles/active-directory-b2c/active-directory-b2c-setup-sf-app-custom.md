---
title: Добавление поставщика SAML Salesforce с помощью пользовательских политик Azure в Active Directory B2C | Документация Майкрософт
description: В этой статье содержатся сведения о создании пользовательских политик Azure Active Directory B2C и управлении ими.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f4399730755c15fe5e171bf7fd5826c2b7ffea0a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709654"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C. Выполнение входа с помощью учетных записей Salesforce через SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается настройка входа для пользователей из определенной организации Salesforce с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>предварительным требованиям

### <a name="azure-ad-b2c-setup"></a>Настройка Azure AD B2C

Убедитесь, что вы выполнили все действия, связанные с [началом работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md) в Azure Active Directory B2C (Azure AD B2C).

в частности такие:

* Создание клиента Azure AD B2C.
* Создание приложения Azure AD B2C.
* Регистрация двух приложений подсистемы политик.
* Настройка ключей.
* Настройка начального пакета.

### <a name="salesforce-setup"></a>Настройка Salesforce

Для выполнения действий, описанных в этой статье, вам необходимо следующее.

* Подписка на учетную запись Salesforce. Вы можете зарегистрироваться для получения [бесплатной учетной записи Developer Edition](https://developer.salesforce.com/signup).
* [Настройка собственного домена](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) для организации Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Настройка Salesforce для федерации пользователей

Чтобы обеспечить взаимодействие Azure AD B2C с Salesforce, необходимо получить URL-адрес метаданных Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Настройка Salesforce в качестве поставщика удостоверений

> [!NOTE]
> В этой статье предполагается, что вы используете [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Войдите в Salesforce](https://login.salesforce.com/). 
2. В меню слева в разделе **Параметры** разверните узел **Удостоверение** и щелкните **Поставщик удостоверений**.
3. Щелкните **Enable Identity Provider** (Включить поставщик удостоверений).
4. В разделе **Select the certificate** (Выберите сертификат) выберите сертификат, который необходимо использовать в Salesforce при взаимодействии с Azure AD B2C. Вы можете использовать сертификат по умолчанию. Выберите команду **Сохранить**. 

### <a name="create-a-connected-app-in-salesforce"></a>Создание подключенного приложения в Salesforce

1. На странице **Поставщик удостоверений** перейдите в раздел **Поставщики услуг**.
2. Щелкните **Service Providers are now created via Connected Apps. Click here** (Поставщики услуг теперь создаются с помощью подключенных приложений. Щелкните здесь).
3. В разделе **Основные сведения** введите нужные значения для подключенного приложения.
4. В разделе **Параметры веб-приложения** установите флажок **Включить SAML**.
5. В поле **Идентификатор сущности** введите следующий URL-адрес. Проверьте, заменено ли значение `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. В поле **ACS URL** (URL-адрес ACS) введите приведенный ниже URL-адрес. Проверьте, заменено ли значение `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Для остальных параметров оставьте значения по умолчанию.
8. Прокрутите до нижней части списка и нажмите кнопку **Сохранить**.

### <a name="get-the-metadata-url"></a>Получить URL-адреса метаданных

1. На странице общих сведений о подключенном приложении щелкните **Управление**.
2. Скопируйте значение **конечной точки обнаружения метаданных**, а затем сохраните его. Оно будет использоваться далее в этой статье.

### <a name="set-up-salesforce-users-to-federate"></a>Настройка пользователей Salesforce для федерации

1. На странице **Управление** подключенного приложения перейдите в раздел **Профили**.
2. Щелкните **Управление профилями**.
3. Выберите профили (или группы пользователей) для федерации с Azure AD B2C. Используя права системного администратора, установите флажок **Системный администратор** для федерации с помощью учетной записи Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Создание сертификата подписи для Azure AD B2C

Запросы, отправляемые в Salesforce, должны быть подписаны Azure AD B2C. Чтобы создать сертификат подписи, откройте Azure PowerShell и выполните следующие команды.

> [!NOTE]
> Обновите имя клиента и пароль в первых двух строках.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Добавление сертификата подписи SAML в Azure AD B2C

Отправьте сертификат подписи в клиент Azure AD B2C: 

1. Перейдите в клиент Azure AD B2C. Последовательно выберите **Settings** > **Identity Experience Framework** > **Policy Keys** (Параметры, Инфраструктура процедур идентификации, Ключи политики).
2. Щелкните **+Добавить**, а затем:
    1. Щелкните **Параметры** > **Отправить**.
    2. Введите **имя** (например, SAMLSigningCert). Префикс *B2C_1A_* будет автоматически добавлен к имени ключа.
    3. Чтобы выбрать сертификат, выберите **элемент управления отправкой файла**. 
    4. Введите пароль сертификата, который задан в скрипте PowerShell.
3. Нажмите кнопку **Создать**.
4. Убедитесь, что ключ создан (например, B2C_1A_SAMLSigningCert). Запишите полное имя (включая *B2C_1A_*). Вы будете ссылаться на этот ключ позднее в политике.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Создание поставщика утверждений SAML Salesforce в базовой политике

Чтобы пользователи могли выполнять вход с помощью Salesforce, необходимо определить Salesforce в качестве поставщика утверждений. Другими словами, необходимо указать конечную точку, с которой будет взаимодействовать Azure AD B2C. Конечная точка *предоставит* набор *утверждений*, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя. Это можно сделать, добавив `<ClaimsProvider>` для Salesforce в файл расширения политики.

1. Откройте файл расширения (TrustFrameworkExtensions.xml) из рабочего каталога.
2. Найдите раздел `<ClaimsProviders>`. Если он не существует, создайте его в корневом узле.
3. Добавьте новый `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

В узле `<ClaimsProvider>`:

1. Укажите для `<Domain>` уникальное значение, позволяющее отличить этот `<ClaimsProvider>` от других.
2. Задайте для `<DisplayName>` отображаемое имя поставщика утверждений. Сейчас это значение не используется.

### <a name="update-the-technical-profile"></a>Обновление технического профиля

Чтобы получить токен SAML из Salesforce, определите протоколы, используемые Azure AD B2C для взаимодействия с Active Directory (Azure AD). Сделайте в элементе `<TechnicalProfile>` поставщика `<ClaimsProvider>` следующее:

1. Обновите идентификатор узла `<TechnicalProfile>`. Этот идентификатор используется для ссылки на этот технический профиль из других частей политики.
2. Обновите значение для `<DisplayName>`. Это значение отображается на кнопке входа на экране входа.
3. Обновите значение для `<Description>`.
4. Salesforce использует протокол SAML 2.0. Убедитесь, что значение для `<Protocol>` — **SAML2**.

Обновите раздел `<Metadata>` в предыдущем коде XML в соответствии с параметрами для определенной учетной записи Salesforce. В коде XML обновите значения метаданных:

1. Измените значение `<Item Key="PartnerEntity">` на скопированный ранее URL-адрес метаданных Salesforce. В нем используется следующий формат: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. В разделе `<CryptographicKeys>` измените значение для обоих экземпляров `StorageReferenceId` на имя ключа сертификата подписи (например, B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

Теперь политика настроена, а Azure AD B2C известно, как взаимодействовать с Salesforce. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке. Чтобы отправить файл расширения политики:

1. В клиенте Azure AD B2C перейдите в колонку **Все политики**.
2. Установите флажок **Перезаписать политику, если она существует**.
3. Отправьте файл расширения (TrustFrameworkExtensions.xml). Убедитесь, что проверка пройдена.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Регистрация поставщика утверждений SAML Salesforce для пути взаимодействия пользователя

Теперь вам необходимо добавить поставщик удостоверений SAML Salesforce в один из путей взаимодействия пользователя. На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одной странице регистрации или входа пользователя. Чтобы добавить поставщик на страницу входа, сначала создайте копию существующего шаблона пути пользователя. Затем измените шаблон, чтобы у него также был поставщик удостоверений Azure AD.

1. Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2. Найдите элемент `<UserJourneys>` и скопируйте полное значение `<UserJourney>`, включая Id="SignUpOrSignIn".
3. Откройте файл расширения (например, TrustFrameworkExtensions.xml). Найдите элемент `<UserJourneys>`. Если элемент не существует, создайте его.
4. Вставьте весь скопированный путь `<UserJourney>` как дочерний элемент `<UserJourneys>`.
5. Переименуйте идентификатор нового пути `<UserJourney>` (например, SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Отображение кнопки поставщика удостоверений

Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на странице регистрации или входа. Если вы добавите для Salesforce элемент `<ClaimsProviderSelection>`, новая кнопка отобразится при переходе пользователя на эту страницу. Чтобы отобразить кнопку поставщика удостоверений:

1. В созданном `<UserJourney>` найдите `<OrchestrationStep>` со значением `Order="1"`.
2. Добавьте следующий код XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Задайте для `TargetClaimsExchangeId` логическое значение. Мы советуем следовать общему соглашению: (например, *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Связывание кнопки поставщика удостоверений с действием

Теперь, когда у вас есть кнопка поставщика удостоверений, свяжите ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Salesforce для получения токена SAML. Чтобы получить эту возможность, необходимо связать технический профиль для поставщика утверждений SAML Salesforce.

1. В узле `<UserJourney>` найдите `<OrchestrationStep>` со значением `Order="2"`.
2. Добавьте следующий код XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Измените `Id` на то же значение, которое ранее использовалось для `TargetClaimsExchangeId`.
4. Задайте для `TechnicalProfileReferenceId` значение `Id` ранее созданного технического профиля (например, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Передача обновленного файла расширения

Вы внесли все необходимые изменения в файл расширения. Сохраните и отправьте этот файл. Убедитесь, что все проверки пройдены успешно.

### <a name="update-the-relying-party-file"></a>Обновление файла проверяющей стороны

Теперь обновите файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию SignUpOrSignIn.xml в рабочем каталоге. Затем переименуйте ее (например, SignUpOrSignInWithAAD.xml).
2. Откройте новый файл и задайте для атрибута `PolicyId` `<TrustFrameworkPolicy>` уникальное значение. Это имя вашей политики (например, SignUpOrSignInWithAAD).
3. Измените атрибут `ReferenceId` в `<DefaultUserJourney>` для соответствия `Id` нового созданного пути взаимодействия пользователя (например, SignUpOrSignUsingContoso).
4. Сохраните изменения и отправьте файл.

## <a name="test-and-troubleshoot"></a>Тестирование и устранение неполадок

Чтобы протестировать отправленную настраиваемую политику, на портале Azure перейдите к колонке политики и нажмите кнопку **Выполнить**. В случае неудачи см. сведения в разделе [Устранение неполадок пользовательских политик](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Дополнительная информация

Свои отзывы отправляйте сюда: [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
