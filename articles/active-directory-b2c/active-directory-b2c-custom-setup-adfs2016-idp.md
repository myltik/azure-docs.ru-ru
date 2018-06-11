---
title: Добавление ADFS в качестве поставщика удостоверений SAML с помощью пользовательских политик в Azure Active Directory B2C | Документация Майкрософт
description: Эта статья представляет собой практическое руководство, в котором объясняется, как настроить ADFS 2016 с помощью протокола SAML и пользовательских политик.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 06b70e48398fd720b6c87cc4575b7a7973f5bce5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709195"
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C. Добавление ADFS в качестве поставщика удостоверений SAML с помощью пользовательских политик

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

В этой статье описывается выполнение входа для пользователей из учетной записи ADFS с помощью [пользовательских политик](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>предварительным требованиям

Выполните шаги, описанные в статье [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md).

А именно:

1.  Создание отношения доверия с проверяющей стороной ADFS.
2.  Добавление сертификата отношения доверия с проверяющей стороной ADFS в Azure AD B2C.
3.  Добавление поставщика утверждений в политику.
4.  Регистрация поставщика утверждений учетной записи ADFS для пути взаимодействия пользователя.
5.  Отправка политики в клиент Azure AD B2C и ее проверка.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Создание отношения доверия с проверяющей стороной, поддерживающей утверждения

Чтобы использовать ADFS в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать отношение доверия с проверяющей стороной ADFS и задать в ней правильные параметры.

Чтобы добавить новое отношение доверия с проверяющей стороной с помощью оснастки управления AD FS и вручную настроить параметры, выполните следующую процедуру на сервере федерации.

Чтобы выполнить эту процедуру, необходимо по крайней мере входить в группу **Администраторы** или аналогичную группу на локальном компьютере. Дополнительные сведения об использовании соответствующих учетных записей и членстве в группах см. в статье [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477) (Локальные и доменные группы по умолчанию).

1.  В диспетчере серверов щелкните **Средства**, а затем выберите **ADFS Management** (Управление ADFS).

2.  Выберите **Add Relying Party Trust** (Добавить отношение доверия с проверяющей стороной).
    ![Добавление отношения доверия проверяющей стороны](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  На странице **приветствия** выберите **Claims aware** (Поддерживающие утверждения) и нажмите кнопку **Начало**.
    ![На странице приветствия выберите Claims aware (Поддерживающие утверждения)](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  На странице **Выберите источник данных** щелкните **Enter data about the relying party manually** (Ввод данных о проверяющей стороне вручную), а затем нажмите кнопку **Далее**.
    ![Ввод данных о проверяющей стороне](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  На странице **Specify Display Name** (Указание отображаемого имени) введите имя в поле **Отображаемое имя**. В поле **Примечания** введите описание для этого отношения доверия с проверяющей стороной и нажмите кнопку **Далее**.
    ![Указание отображаемого имени и примечаний](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Необязательный элемент. Если у вас есть необязательный сертификат для шифрования маркера, на странице **Configure Certificate** (Настройка сертификата) щелкните **Обзор** для поиска файла сертификата, а затем нажмите кнопку **Далее**.
    ![Настройка сертификата](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  На странице **Configure URL** (Настройка URL-адреса) установите флажок **Enable support for the SAML 2.0 WebSSO protocol** (Включить поддержку протокола SAML 2.0 WebSSO). В поле **Relying party SAML 2.0 SSO service URL** (URL-адрес службы SAML 2.0 SSO проверяющей стороны) введите URL-адрес конечной точки службы SAML для этого отношения доверия с проверяющей стороной и нажмите кнопку **Далее**.  Вставьте `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}` в поле **Relying party SAML 2.0 SSO service URL** (URL-адрес службы SAML 2.0 SSO проверяющей стороны). Замените {tenant} на имя вашего клиента (например, contosob2c.onmicrosoft.com), а {policy} — на имя политики расширений (например, B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Именем политики является имя, которое наследуется от политики signup_or_signin. В данном случае это `B2C_1A_TrustFrameworkExtensions`.
    >Например, URL-адресом может быть https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![URL-адрес службы SAML 2.0 SSO проверяющей стороны](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. На странице **Configure Identifiers** (Настройка идентификатора) укажите тот же URL-адрес, нажмите кнопку **Добавить**, чтобы добавить его в список, а затем — **Далее**.
    ![Идентификаторы отношений доверия проверяющей стороны](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  На странице **Choose Access Control Policy** (Выбрать политику управления доступом) выберите политику и нажмите кнопку **Далее**.
    ![Выбор политики управления доступом](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  На странице **Ready to Add Trust** (Готовность для добавления отношения доверия) проверьте параметры и нажмите кнопку **Далее**, чтобы сохранить сведения об отношении доверия с проверяющей стороной.
    ![Сохранение сведений об отношении доверия с проверяющей стороной](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  На странице **Готово** щелкните **Закрыть**. При этом автоматически откроется диалоговое окно **Edit Claim Rules** (Изменение правил утверждений).
    ![Изменение правил утверждений](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Щелкните **Добавить правило**.  
      ![Добавление нового правила](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  В разделе **Claim rule template** (Шаблон правила утверждения) выберите **Send LDAP attributes as claims** (Отправка атрибутов LDAP как утверждений).
    ![Выбор шаблона правила "Отправка атрибутов LDAP как утверждений"](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Укажите **имя правила утверждения**. В разделе **Attribute store** (Хранилище атрибутов) выберите **Active Directory**, добавьте следующие утверждения, а затем нажмите кнопку **Готово** и **ОК**.
    ![Настройка свойств правила](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  В диспетчере серверов выберите **Relying Party Trusts** (Отношения доверия с проверяющей стороной), а затем выберите созданное отношение доверия с проверяющей стороной и щелкните **Свойства**.
    ![Изменение свойств проверяющей стороны](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  В окне свойств отношения доверия с проверяющей стороной (демоверсия B2C) перейдите на вкладку **Подпись** и нажмите кнопку **Добавить**.  
    ![Настройка подписи](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Добавьте сертификат подписи (CERT-файл без закрытого ключа).  
    ![Добавление сертификата подписи](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  В окне свойств отношения доверия с проверяющей стороной (демоверсия B2C) перейдите на вкладку **Дополнительно**, измените **алгоритм SHA** на **SHA-1** и нажмите кнопку **ОК**.  
    ![Изменение алгоритма SHA на SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Добавление ключа приложения учетной записи ADFS в Azure AD B2C
Для федерации с учетными записями ADFS требуется секрет клиента учетной записи ADFS, чтобы доверять Azure AD B2C от имени приложения. Вам необходимо сохранить сертификат ADFS в клиенте Azure AD B2C. 

1.  Перейдите к клиенту Azure AD B2C и выберите **B2C Settings** (Параметры B2C)  > **Identity Experience Framework**.
2.  Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3.  Щелкните **+Добавить**.
4.  В пункте **Параметры** используйте вариант **Отправить**.
5.  Для параметра **Имя** используйте значение `ADFSSamlCert`.  
    Префикс `B2C_1A_` может быть добавлен автоматически.
6.  В поле "Отправка файлов" \*\* выберите PFX-файл сертификата с закрытым ключом. Примечание. Это должен быть сертификат (с закрытым ключом), выданный и использованный для проверяющей стороны ADFS.
![Отправка ключа политики](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Нажмите кнопку **Создать**.
8.  Убедитесь, что вы создали ключ `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Добавление поставщика утверждений в политику расширения
Чтобы пользователи выполняли вход с помощью учетной записи ADFS, необходимо определить учетную запись ADFS в качестве поставщика утверждений. Другими словами, необходимо указать конечную точку, с которой взаимодействует Azure AD B2C. Конечная точка предоставляет набор утверждений, используемых Azure AD B2C, чтобы проверить, была ли выполнена проверка подлинности определенного пользователя.

Определите ADFS в качестве поставщика утверждений, добавив узел `<ClaimsProvider>` в файл политики расширения.

1. Откройте файл политики расширения (TrustFrameworkExtensions.xml) из рабочего каталога. Если вам нужен редактор XML, [попробуйте Visual Studio Code](https://code.visualstudio.com/download) — упрощенный кроссплатформенный редактор.
2. Найдите раздел `<ClaimsProviders>`.
3. Добавьте следующий фрагмент XML-кода в элемент `ClaimsProviders`, замените `identityProvider` на DNS (произвольное значение, указывающее домен) и сохраните файл. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Регистрация поставщика утверждений учетной записи ADFS для пути взаимодействия пользователя "Регистрация" или "Вход"
На этом этапе поставщик удостоверений настроен.  Однако он недоступен ни на одном из экранов регистрации или входа. Теперь необходимо добавить поставщик удостоверений учетной записи ADFS для пути взаимодействия пользователя `SignUpOrSignIn`. Чтобы сделать его доступным, необходимо создать дубликат имеющегося шаблона пути взаимодействия пользователя,  а затем изменить его таким образом, чтобы он содержал поставщик удостоверений ADFS.

>[!NOTE]
>Если ранее вы скопировали элемент `<UserJourneys>` из основного файла политики в файл расширения (TrustFrameworkExtensions.xml), можно пропустить этот раздел.

1.  Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2.  Найдите элемент `<UserJourneys>` и скопируйте все содержимое узла `<UserJourneys>`.
3.  Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<UserJourneys>`. Если элемент не существует, добавьте его.
4.  Вставьте весь скопированный узел `<UserJournesy>` как дочерний узел элемента `<UserJourneys>`.

### <a name="display-the-button"></a>Отображение кнопки
Элемент `<ClaimsProviderSelections>` определяет список параметров выбора поставщика утверждений и их порядок.  Элемент `<ClaimsProviderSelection>` является аналогом кнопки поставщика удостоверений на странице регистрации или входа. Если вы добавите для учетной записи ADFS элемент `<ClaimsProviderSelection>`, при переходе пользователя на страницу отобразится новая кнопка. Для этого:

1.  Найдите узел `<UserJourney>`, содержащий `Id="SignUpOrSignIn"` в скопированном пути пользователя.
2.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
3.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием

Теперь, когда у вас есть кнопка, вам необходимо связать ее с действием. В этом случае действие — это возможность взаимодействия Azure AD B2C с учетной записью ADFS для получения маркера. Свяжите кнопку с действием, связав технический профиль для поставщика утверждений учетной записи ADFS.

1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Убедитесь, что `Id` имеет то же значение, что и `TargetClaimsExchangeId` в предыдущем разделе.
> * Убедитесь, что для `TechnicalProfileReferenceId` задан ранее созданный технический профиль (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Откройте колонку **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="test-the-custom-policy-by-using-run-now"></a>Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.
2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  Вы можете войти с использованием учетной записи ADFS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Необязательно] Регистрация поставщика утверждений учетной записи ADFS для пути взаимодействия пользователя "Изменение профиля"
Вы также можете добавить поставщик удостоверений учетной записи ADFS для пути взаимодействия пользователя `ProfileEdit`. Чтобы сделать его доступным, необходимо повторить последние два шага:

### <a name="display-the-button"></a>Отображение кнопки
1.  Откройте файл расширения политики (например, TrustFrameworkExtensions.xml).
2.  Найдите узел `<UserJourney>`, содержащий `Id="ProfileEdit"` в скопированном пути пользователя.
3.  Найдите узел `<OrchestrationStep>`, содержащий `Order="1"`.
4.  Добавьте в узел `<ClaimsProviderSelections>` следующий фрагмент XML-кода:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Связывание кнопки с действием
1.  Найдите `<OrchestrationStep>`, который содержит `Order="2"` в узле `<UserJourney>`.
2.  Добавьте в узел `<ClaimsExchanges>` следующий фрагмент XML-кода:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Тестирование пользовательской политики изменения профиля с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.
2.  Откройте **B2C_1A_ProfileEdit**, отправленную пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  Вы можете войти с использованием учетной записи ADFS.

## <a name="download-the-complete-policy-files"></a>Загрузка завершенных файлов политики
Необязательно. Мы советуем создать свой сценарий, используя собственные файлы пользовательской политики, после того как вы ознакомитесь с пошаговым руководством по началу работы с пользовательскими политиками. [Файлы примеров политики, предназначенные только для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
