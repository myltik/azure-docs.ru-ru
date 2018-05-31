---
title: Azure Active Directory B2C. Добавление поставщика Azure AD с помощью пользовательских политик | Документы Майкрософт
description: Сведения о пользовательских политиках Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 40943c135746925929daf7ebae4714ef70eeda51
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140229"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C. Выполнение входа с помощью учетных записей Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается включение входа для пользователей из определенной организации Azure Active Directory (Azure AD) с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>предварительным требованиям

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

А именно:

1. Создание клиента Azure Active Directory B2C (Azure AD B2C).
2. Создание приложения Azure AD B2C.
3. Регистрация двух приложений подсистемы политик.
4. Настройка ключей.
5. Настройка начального пакета.

## <a name="create-an-azure-ad-app"></a>Создание приложения Azure AD

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте организации Azure AD.

>[!NOTE]
> В следующих инструкциях клиент организации Azure AD будет называться "contoso.com", а клиент Azure AD B2C — "fabrikamb2c.onmicrosoft.com".

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели выберите учетную запись. В списке **Каталог** выберите клиент организации Azure AD, в котором вы хотите зарегистрировать приложение (например, contoso.com).
3. В левой области щелкните **Больше служб** и выполните поиск по запросу "регистрация приложений".
4. Выберите **Регистрация нового приложения**.
5. Введите значение имя для приложения (например, `Azure AD B2C App`).
6. Выберите в качестве типа приложения **Веб-приложение или API**.
7. В поле **URL-адрес входа** введите следующий URL-адрес, где `yourtenant` заменяется на имя вашего клиента Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Значение для yourtenant в поле **URL-адрес входа** следует указывать в нижнем регистре.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. Сохраните идентификатор приложения.
9. Щелкните созданное приложение.
10. В колонке **Параметры** щелкните **Ключи**.
11. Введите описание ключа, выберите длительность и нажмите кнопку **Сохранить**. Появится значение ключа. Скопируйте его, так как он понадобится для выполнения действий в следующем разделе.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Добавление ключа Azure AD в Azure AD B2C

Вам необходимо сохранить ключ приложения contoso.com в клиенте Azure AD B2C. Для этого:
1. Перейдите к клиенту Azure AD B2C и последовательно выберите **B2C Settings** > **Identity Experience Framework** > **Policy Keys** (Параметры B2C > Инфраструктура процедур идентификации > Ключи политики).
1. Щелкните **+Добавить**.
1. Выберите или введите следующие параметры:
   * Выберите **Вручную**.
   * Для параметра **Имя** выберите имя, соответствующее имени клиента Azure AD (например, `ContosoAppSecret`).  Префикс `B2C_1A_` будет автоматически добавлен к имени ключа.
   * Вставьте ключ приложения в текстовое поле **Секрет**.
   * Выберите **Подпись**.
1. Нажмите кнопку **Создать**.
1. Убедитесь, что вы создали ключ `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Добавление поставщика утверждений в базовую политику

Чтобы пользователи выполняли вход с помощью Azure AD, необходимо определить Azure AD в качестве поставщика утверждений. Другими словами, необходимо указать конечную точку, с которой будет взаимодействовать Azure AD B2C. Конечная точка предоставит набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя. 

Можно определить Azure AD в качестве поставщика утверждений, добавив Azure AD в узел `<ClaimsProvider>` в файл расширения политики.

1. Откройте файл расширения (TrustFrameworkExtensions.xml) из рабочего каталога.
1. Найдите раздел `<ClaimsProviders>`. Если он не существует, добавьте его в корневой узел.
1. Добавьте новый `<ClaimsProvider>`, как показано ниже.

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
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

1. В узле `<ClaimsProvider>` укажите для `<Domain>` уникальное значение, позволяющее отличить этот поставщик удостоверений от других.
1. В узле `<ClaimsProvider>` задайте для `<DisplayName>` понятное имя поставщика утверждений. В настоящее время это значение не используется.

### <a name="update-the-technical-profile"></a>Обновление технического профиля

Чтобы получить токен из конечной точки Azure AD, вам необходимо определить протоколы, используемые Azure AD B2C для взаимодействия с Azure AD. Этот процесс происходит внутри элемента `<TechnicalProfile>` поставщика `<ClaimsProvider>`.
1. Обновите идентификатор узла `<TechnicalProfile>`. Этот идентификатор используется для ссылки на этот технический профиль из других частей политики.
1. Обновите значение для `<DisplayName>`. Это значение будет отображаться на кнопке входа на экране входа в систему.
1. Обновите значение для `<Description>`.
1. Azure AD использует протокол OpenID Connect, поэтому для `<Protocol>` должно быть задано значение `"OpenIdConnect"`.

Вам следует обновить раздел `<Metadata>` в приведенном выше коде XML, чтобы добавить параметры конфигурации для определенного клиента Azure AD. В XML-файле обновите значения метаданных следующим образом:

1. Задайте для параметра `<Item Key="METADATA">` значение `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, где `yourAzureADtenant` — это имя клиента Azure AD (contoso.com).
1. Откройте браузер и перейдите к URL-адресу `METADATA`, который вы только что обновили.
1. Найдите на этой странице в браузере объект issuer и скопируйте его значение. Вы должны увидеть примерно следующее: `https://sts.windows.net/{tenantId}/`.
1. Вставьте значение для `<Item Key="ProviderName">` в XML-файл.
1. Задайте для параметра `<Item Key="client_id">` значение идентификатора приложения.
1. Задайте для параметра `<Item Key="IdTokenAudience">` значение идентификатора приложения.
1. Задайте для параметра `<Item Key="response_types">` значение `id_token`.
1. Задайте для параметра `<Item Key="UsePolicyInRedirectUri">` значение `false`.

Вам также необходимо связать секрет Azure AD, зарегистрированный в клиенте Azure AD B2C, с информацией о `<ClaimsProvider>` Azure AD.

* В разделе `<CryptographicKeys>` в приведенном выше XML-файле задайте для `StorageReferenceId` эталонный идентификатор определенного секрета (например, `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Отправка файла расширения для проверки

К этому моменту политика настроена, так что Azure AD B2C знает, как взаимодействовать с каталогом Azure AD. Попробуйте отправить файл расширения политики, чтобы убедиться, что все в порядке. Для этого выполните следующие действия:

1. Откройте колонку **Все политики** в клиенте Azure AD B2C.
1. Установите флажок **Перезаписать политику, если она существует**.
1. Отправьте файл расширения (TrustFrameworkExtensions.xml) и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Регистрация поставщика утверждений Azure AD для пути взаимодействия пользователя

Теперь вам необходимо добавить поставщик удостоверений Azure AD в один из путей взаимодействия пользователя. На этом этапе поставщик удостоверений уже настроен, но еще не доступен ни на одном экране регистрации или входа. Чтобы сделать его доступным, необходимо создать дубликат существующего шаблона пути взаимодействия пользователя, а затем изменить его таким образом, чтобы он также содержал поставщик удостоверений Azure AD.

1. Откройте базовый файл политики (например, TrustFrameworkBase.xml).
1. Найдите элемент `<UserJourneys>` и скопируйте полный узел `<UserJourney>` с `Id="SignUpOrSignIn"`.
1. Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
1. Вставьте весь скопированный узел `<UserJourney>` как дочерний элемент `<UserJourneys>`.
1. Переименуйте идентификатор нового пути пользователя (например, переименуйте как `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Отображение кнопки

Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на экране регистрации или входа. Если вы добавите для Azure AD элемент `<ClaimsProviderSelection>`, новая кнопка отобразится при переходе пользователя на страницу. Для этого:

1. Найдите узел `<OrchestrationStep>`, который содержит `Order="1"` в только что созданном пути пользователя.
1. Добавьте следующий код:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Задайте для параметра `TargetClaimsExchangeId` соответствующее значение. Мы советуем следовать общему соглашению: *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с Azure AD для получения токена. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений Azure AD.

1. Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
1. Добавьте следующий код:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Обновите `Id` до того же значения, которое имеет `TargetClaimsExchangeId` в предыдущем разделе.
1. Задайте для `TechnicalProfileReferenceId` значение идентификатора ранее созданного технического профиля (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Передача обновленного файла расширения

Вы внесли все необходимые изменения в файл расширения. Сохраните его. Затем отправьте этот файл. Подождите немного, чтобы убедиться, что все проверки завершены успешно.

### <a name="update-the-rp-file"></a>Обновление файла проверяющей стороны

Теперь необходимо обновить файл проверяющей стороны, который активирует созданный путь взаимодействия пользователя.

1. Создайте копию SignUpOrSignIn.xml в рабочем каталоге и переименуйте его (например, на SignUpOrSignInWithAAD.xml).
1. Откройте новый файл и задайте для атрибута `PolicyId` для `<TrustFrameworkPolicy>` новое уникальное значение (например, SignUpOrSignInWithAAD). <br> Это будет имя вашей политики (например, B2C\_1A\_SignUpOrSignInWithAAD).
1. Задайте для атрибута `ReferenceId` в `<DefaultUserJourney>` идентификатор созданного пути взаимодействия пользователя (например, SignUpOrSignUsingContoso).
1. Сохраните изменения и отправьте файл.

## <a name="troubleshooting"></a>Устранение неполадок

Проверьте пользовательскую политику, которую вы отправили. Для этого откройте ее колонку и щелкните **Запустить сейчас**. Для диагностики проблем ознакомьтесь со статьей по [устранению неполадок](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Дополнительная информация

Свои отзывы отправляйте сюда: [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
