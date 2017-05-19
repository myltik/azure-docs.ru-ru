---
title: "Azure Active Directory B2C. Добавление поставщика SAML Salesforce с помощью пользовательских политик | Документация Майкрософт"
description: "В этой статье описываются пользовательские политики Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: dee24deacbe69ada64519802c0eb1b83f565f57e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C. Выполнение входа с помощью учетных записей Salesforce через SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается выполнение входа для пользователей из определенной организации Salesforce с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Предварительные требования

### <a name="azure-ad-b2c-setup"></a>Настройка Azure AD B2C
Убедитесь, что вы выполнили все действия, связанные с [началом работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md).

А именно:

1. Создание клиента Azure AD B2C.
1. Создание приложения Azure AD B2C.
1. Регистрация двух приложений подсистемы политик.
1. Настройка ключей.
1. Настройка начального пакета.

### <a name="salesforce-setup"></a>Настройка Salesforce
Для выполнения действий, описанных в этом учебнике, вам необходимо следующее.
1. Подписка на учетную запись Salesforce. Вы можете подписаться на [бесплатный выпуск Developer Edition](https://developer.salesforce.com/signup). 
1. [Настройка собственного домена](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) для организации Salesforce.

## <a name="get-the-salesforce-saml-metadata"></a>Получение метаданных SAML Salesforce
>[!NOTE]
> В этом руководстве предполагается, что вы используете [Salesforce Lighting Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Войдите в Salesforce](https://login.salesforce.com/). 
1. В меню слева в разделе **Параметры** выберите **Удостоверение** и щелкните **Поставщик удостоверений**.
1. Щелкните **Enable Identity Provider** (Включить поставщик удостоверений).
1. **Выберите сертификат**, который необходимо использовать в Salesforce при взаимодействии с Azure AD B2C, и нажмите кнопку **Сохранить**. Вы можете использовать сертификат по умолчанию.
1. Нажмите кнопку **Download Metadata** (Скачать метаданные) и сохраните файл метаданных. Он потребуется вам в дальнейшем.

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>Добавление сертификата для подписи SAML в Azure AD B2C
Вам необходимо отправить сертификат Salesforce в клиент Azure AD B2C. Для этого:

1. Откройте PowerShell и перейдите в рабочий каталог `active-directory-b2c-advanced-policies`.
1. Перейдите в папку со средством ExploreAdmin.

    ```powershell
    cd active-directory-b2c-advanced-policies\ExploreAdmin
    ```

1. Импортируйте средство ExploreAdmin в PowerShell.

    ```powershell
    Import-Module .\ExploreAdmin.dll
    ```

1. В команде ниже замените `tenantName` именем клиента Azure AD B2C (например, fabrikamb2c.onmicrosoft.com), а `certificateId` — именем сертификата, которое вам понадобится позже для ссылки (например, ContosoSalesforceCert). `pathToCert` и `password` замените путем и паролем сертификата. Выполните команду.

    ```PowerShell
    Set-CpimCertificate -TenantId {tenantName} -CertificateId {certificateId} -CertificateFileName {pathToCert} - CertificatePassword {password}
    ```

    При выполнении команды вход необходимо выполнить с помощью локальной учетной записи администратора onmicrosoft.com клиента Azure AD B2C. 

1. Закройте PowerShell.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Создание поставщика утверждений SAML Salesforce в базовой политике

Чтобы разрешить пользователям выполнять вход с помощью Salesforce, необходимо определить Salesforce в качестве поставщика утверждений. Другими словами, необходимо указать конечную точку, с которой будет взаимодействовать Azure AD B2C. Конечная точка *предоставит* набор *утверждений*, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя. Это можно сделать, добавив `<ClaimsProvider>` Salesforce в файл расширения политики.

1. Откройте файл расширения из рабочего каталога (TrustFrameworkExtensions.xml).
1. Найдите раздел `<ClaimsProviders>`. Если он не существует, добавьте его в корневой узел.
1. Добавьте новый `<ClaimsProvider>`, как показано ниже.

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
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

1. В узле `<ClaimsProvider>` укажите для `<Domain>` уникальное значение, позволяющее отличить этот поставщик удостоверений.
1. В узле `<ClaimsProvider>` задайте для `<DisplayName>` понятное имя поставщика утверждений. В настоящее время это значение не используется.

### <a name="update-the-technical-profile"></a>Обновление технического профиля

Чтобы получить токен SAML из Salesforce, вам необходимо определить протоколы, используемые Azure AD B2C для взаимодействия с Azure AD. Этот процесс происходит внутри элемента `<TechnicalProfile>` поставщика `<ClaimsProvider>`.

1. Обновите идентификатор узла `<TechnicalProfile>`. Этот идентификатор используется для ссылки на этот технический профиль из других частей политики.
1. Обновите значение для `<DisplayName>`. Это значение будет отображаться на кнопке входа на экране входа в систему.
1. Обновите значение для `<Description>`.
1. Azure AD использует протокол OpenID Connect, поэтому для `<Protocol>` должно быть задано значение SAML2.

Вам следует обновить раздел `<Metadata>` в приведенном выше коде XML, чтобы добавить параметры конфигурации для определенного клиента Azure AD. В коде XML обновите значения метаданных следующим образом:

1. Обновите значение `<Item Key="PartnerEntity">`, использовав содержимое файла Metadata.xml, скачанного из Salesforce. **Убедитесь, что оно инкапсулировано в <![CDATA[ …metadata… ]]>**.

1. В разделе `<CryptographicKeys>` в приведенном выше коде XML задайте для `StorageReferenceId` определенный идентификатор сертификата (например, ContosoSalesforceCert).

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

К этому моменту политика будет настроена, так что Azure AD B2C будет знать, как взаимодействовать с каталогом Azure AD. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке. Для этого выполните следующие действия:

1. Перейдите в колонку **Все политики** в клиенте Azure AD B2C.
1. Установите флажок для параметра **Перезаписать политику, если она существует**.
1. Отправьте файл расширения (TrustFrameworkExtensions.xml) и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Регистрация поставщика утверждений SAML Salesforce для пути взаимодействия пользователя

Теперь вам необходимо добавить поставщик удостоверений SAML Salesforce в один из путей взаимодействия пользователя. На этом этапе поставщик удостоверений уже настроен, но еще недоступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат имеющегося шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений Azure AD.

1. Откройте базовый файл политики (например, TrustFrameworkBase.xml).
1. Найдите элемент `<UserJourneys>` и скопируйте полный `<UserJourney>` с Id=”SignUpOrSignIn”.
1. Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
1. Вставьте весь скопированный `<UserJourney>` как дочерний элемент `<UserJourneys>`.
1. Переименуйте идентификатор нового `<UserJourney>` (например, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Отображение кнопки

Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если вы добавите для Salesforce элемент `<ClaimsProviderSelection>`, новая кнопка отобразится при переходе пользователя на страницу. Для этого:

1. Найдите `<OrchestrationStep>` со значением `Order="1"` в только что созданном `<UserJourney>`.
1. Добавьте следующий код:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Задайте для параметра `TargetClaimsExchangeId` соответствующее значение. Мы советуем следовать общему соглашению: *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Salesforce для получения токена SAML. Чтобы получить эту возможность, необходимо связать технический профиль для поставщика утверждений SAML Salesforce.

1. Найдите `<OrchestrationStep>` со значением `Order="2"` в узле `<UserJourney>`.
1. Добавьте следующий код:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Задайте для `Id` то же значение, что и для `TargetClaimsExchangeId` выше.
1. Задайте для `TechnicalProfileReferenceId` значение `Id` ранее созданного технического профиля (например, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Передача обновленного файла расширения

Вы внесли все необходимые изменения в файл расширения. Сохраните и отправьте этот файл. Подождите немного, чтобы убедиться, что все проверки завершены успешно.

### <a name="update-the-rp-file"></a>Обновление файла проверяющей стороны

Теперь необходимо обновить файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию SignUpOrSignIn.xml в рабочем каталоге и переименуйте его (например, на SignUpOrSignInWithAAD.xml).
1. Откройте новый файл и задайте для атрибута `PolicyId` `<TrustFrameworkPolicy>` уникальное значение. Это будет имя политики (например, SignUpOrSignInWithAAD).
1. Задайте для атрибута `ReferenceId` в `<DefaultUserJourney>` идентификатор созданного пути взаимодействия пользователя (например, SignUpOrSignUsingContoso).
1. Сохраните изменения и отправьте файл.

## <a name="create-a-connected-app-in-salesforce"></a>Создание подключенного приложения в Salesforce
В Salesforce необходимо зарегистрировать Azure AD B2C как подключенное приложение.

1. [Войдите в Salesforce](https://login.salesforce.com/). 
1. В меню слева в разделе **Параметры** выберите **Удостоверение** и щелкните **Поставщик удостоверений**.
1. В нижней части раздела **Service Providers** (Поставщики услуг) щелкните **Service Providers are now created via Connected Apps. Click here** (Поставщики услуг теперь создаются с помощью подключенных приложений. Щелкните здесь).
1. Укажите необходимые **основные сведения** для подключенного приложения.
1. Теперь в разделе **Параметры веб-приложения** выполните следующие действия:
    1. Установите флажок **Включить SAML**.
    1. Введите приведенный ниже URL-адрес в поле **Идентификатор сущности**. Проверьте, заменен ли `tenantName`. 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base
        ```

    1. Введите приведенный ниже URL-адрес в поле **ACS URL** (URL-адрес ACS). Проверьте, заменен ли `tenantName`. 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_base/samlp/sso/assertionconsumer
        ```

    1. Для других параметров оставьте значения по умолчанию.
1. Прокрутите к нижней части страницы и нажмите кнопку **Сохранить**.


## <a name="troubleshooting"></a>Устранение неполадок

Проверьте пользовательскую политику, которую вы отправили. Для этого откройте ее колонку и щелкните "Запустить сейчас". В случае возникновения каких-либо ошибок ознакомьтесь со статьей [по устранению неполадок](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Дальнейшие действия
 
Свои отзывы отправляйте сюда: [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).


