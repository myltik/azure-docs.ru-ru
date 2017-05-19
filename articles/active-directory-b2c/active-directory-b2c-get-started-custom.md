---
title: "Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками | Документация Майкрософт"
description: "Как начать работу с настраиваемыми политиками Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: b72db6a0eb8a8621be5f05da6028615d5d24ba1e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

После выполнения действий, описанных в этой статье, настраиваемая политика будет поддерживать регистрацию и вход с помощью локальной учетной записи с использованием электронной почты и пароля. Кроме того, будет подготовлена среда для добавления дополнительных поставщиков удостоверений (например, Facebook или Azure AD).  Эти действия нужно выполнить до внедрения остальных вариантов использования инфраструктуры процедур идентификации Azure AD B2C и многих основных концепций.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, убедитесь, что у вас есть клиент Azure AD B2C. Клиент Azure AD B2C — это контейнер для всех пользователей, приложений, политик и т. д. Если у вас нет клиента, его необходимо [создать](active-directory-b2c-get-started.md). Прежде чем продолжить, мы настоятельно советуем всем разработчикам ознакомиться с руководствами по работе с внедренными политиками Azure AD B2C и настроить приложения на основе этих политик.  Приложения поддерживают оба типа политик. Вы можете изменить имя политики, чтобы вызвать настраиваемую политику.

Чтобы получить разрешение на изменение настраиваемых политик, необходимо связать с клиентом действующую подписку Azure.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Добавление ключей подписывания и шифрования в клиент B2C для использования настраиваемыми политиками

1. Перейдите к колонке Identity Experience Framework (Инфраструктура процедур идентификации) в параметрах вашего клиента Azure AD B2C.
2. Выберите **Policy Keys** (Ключи политики), чтобы просмотреть доступные в клиенте ключи.
3. Создайте `B2C_1A_TokenSigningKeyContainer`, если он не существует:
 * Щелкните **Добавить**.
 * Выберите "Параметры" > `Generate`.
 * Введите имя `TokenSigningKeyContainer`. Префикс B2C_1A_ может добавляться автоматически.
 * В качестве типа ключа выберите `RSA`.
 * Используйте значения даты по умолчанию.
 * Для параметра "Использование ключа" задайте значение `Signature`.
 * Нажмите кнопку **Создать**
4. Создайте `B2C_1A_TokenEncryptionKeyContainer`, если он не существует:
 * Щелкните **Добавить**.
 * Выберите "Параметры" > `Generate`.
 * Введите имя `TokenEncryptionKeyContainer`. Префикс B2C_1A_ может добавляться автоматически.
 * В качестве типа ключа выберите `RSA`.
 * Используйте значения даты по умолчанию.
 * Для параметра "Использование ключа" задайте значение `Encryption`.
 * Нажмите кнопку **Создать**
5. Создайте `B2C_1A_FacebookSecret`. При наличии секретного кода приложения Facebook добавьте его как ключ политики в клиенте.  В противном случае создайте ключ со значением-заполнителем. Это позволит политикам проходить проверку.
 * Щелкните **Добавить**.
 * Выберите "Параметры" > `Manual`.
 * Введите имя `FacebookSecret`. Префикс B2C_1A_ может добавляться автоматически.
 * Выберите "Секрет". Введите значение FacebookSecret, указанное на сайте developers.facebook.com, или задайте значение 0 в качестве заполнителя. *Это не идентификатор приложения Facebook.*
 * Для параметра "Использование ключа" задайте значение "Подпись".
 * Нажмите кнопку **Создать** и подтвердите создание.

## <a name="register-identity-experience-framework-applications"></a>Регистрация приложений инфраструктуры процедур идентификации

Для Azure AD B2C необходимо зарегистрировать два дополнительных приложения, которые используются подсистемой для регистрации и входа пользователей.

>[!NOTE]
>Создайте два приложения, обеспечивающие поддержку входа с помощью локальных учетных записей: IdentityExperienceFramework (веб-приложение) и ProxyIdentityExperienceFramework (встроенное приложение) с делегированным разрешением IdentityExperienceFramework. Локальные учетные записи хранятся только в клиенте. Пользователи могут войти в систему, используя уникальную комбинацию "электронная почта:пароль", чтобы получить доступ к зарегистрированным в клиенте приложениям.

### <a name="create-the-identityexperienceframework-application"></a>Создание приложения IdentityExperienceFramework

1. На [портале Azure](https://portal.azure.com) [переключитесь в контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Откройте колонку `Azure Active Directory` (а не колонку Azure AD B2C). Чтобы ее найти, щелкните **> More Services** (> Больше служб).
1. Щелкните **Регистрация приложений**.
1. Щелкните **+ New application registration** (+ Регистрация нового приложения).
   * Имя: `IdentityExperienceFramework`.
   * Тип приложения: `Web app/API`.
   * URL-адрес входа: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, где `yourtenant` — это доменное имя клиента Azure AD B2C.
1. Щелкните **Создать**.
1. После этого выберите созданное приложение `IdentityExperienceFramework`, щелкните **Свойства**, скопируйте идентификатор приложения и сохраните его для последующего использования.

### <a name="create-the-proxy-identity-experience-framework-application"></a>Создание приложения прокси инфраструктуры процедур идентификации

1. Щелкните **Регистрация приложений**.
1. Щелкните **+ New application registration** (+ Регистрация нового приложения).
   * Имя: `ProxyIdentityExperienceFramework`.
   * Тип приложения: `Native`.
   * URL-адрес входа: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, где `yourtenant` — это клиент Azure AD B2C.
1. Щелкните **Создать**.
1. После этого выберите приложение `ProxyIdentityExperienceFramework`, щелкните **Свойства**, скопируйте идентификатор приложения и сохраните его для последующего использования.
1. Выберите **Необходимые разрешения**, щелкните **+ Add** (+ Добавить), а затем **Выбор API**.
1. Найдите имя `IdentityExperienceFramework`, выберите IdentityExperienceFramework в результатах и щелкните **Выбрать**.
1. Установите флажок рядом с `Access IdentityExperienceFramework`, а затем щелкните **Выбрать**.
1. Нажмите кнопку **Done**(Готово).
1. Щелкните **Предоставить разрешения** и выберите **Да**.

## <a name="download-starter-pack-and-modify-policies"></a>Скачивание начального пакета и изменение политик

Настраиваемые политики — это набор XML-файлов, которые нужно отправить в клиент Azure AD B2C. Мы предоставляем начальные пакеты, которые позволяют быстро приступить к работе. Каждый приведенный ниже начальный пакет содержит минимальное необходимое число технических профилей и путей взаимодействия пользователей для реализации описанных сценариев:
 * LocalAccounts. Позволяет использовать только локальные учетные записи.
 * SocialAccounts. Позволяет использовать только учетные записи социальных сетей (или федеративные).
 * **SocialAndLocalAccounts**. Этот пакет используется в рамках руководства.
 * SocialAndLocalAccountsWithMFA. Позволяет использовать учетные записи социальных сетей, локальные учетные записи и параметры MFA.

Каждый начальный пакет содержит:

* [Базовый файл](active-directory-b2c-overview-custom.md#policy-files) политики. Для базового файла требуется несколько изменений.
* [Файл расширения](active-directory-b2c-overview-custom.md#policy-files) политики.  В этот файл вносится большинство изменений конфигурации.
* [Файлы проверяющей стороны.](active-directory-b2c-overview-custom.md#policy-files) Эти файлы вызывает приложение для выполнения определенных задач.

>[!NOTE]
>Если в редакторе XML поддерживается проверка, можно проверить файлы, используя XML-файл схемы `TrustFrameworkPolicy_0.3.0.0.xsd` в корневой папке начального пакета. При проверке по схеме XML определяются ошибки перед отправкой.

Давайте приступим.

1. Скачайте active-directory-b2c-custom-policy-starterpack из репозитория GitHub.  [Скачайте ZIP-файл](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) или выполните следующую команду:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Откройте папку `SocialAndLocalAccounts`.  В базовом файле (`TrustFrameworkBase.xml`) этой папки приводится содержимое, необходимое для локальных и корпоративных учетных записей или учетных записей социальной сети. Содержимое из социальных сетей не влияет на настройку и запуск локальных учетных записей.
3. Откройте `TrustFrameworkBase.xml`.  Если вам нужен редактор XML, попробуйте [Visual Studio Code](https://code.visualstudio.com/download) (упрощенный кроссплатформенный редактор).
4. В корневом элементе `TrustFrameworkPolicy` обновите атрибуты `TenantId` и `PublicPolicyUri`, заменив `yourtenant.onmicrosoft.com` доменным именем клиента Azure AD B2C.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
>[!NOTE]
>`PolicyId` — это имя политики, которое будет отображаться на портале, и имя, по которому другие файлы политики будут ссылаться на этот файл политики.

5. Сохраните файл.
6. Откройте `TrustFrameworkExtensions.xml` и внесите те же два изменения, заменив `yourtenant.onmicrosoft.com` клиентом Azure AD B2C. Замените также значение `<TenantId>` (в результате вы внесете 3 изменения).  Сохраните файл.
7. Откройте `SignUpOrSignIn.xml` и внесите те же изменения, заменив `yourtenant.onmicrosoft.com` клиентом Azure AD B2C в трех местах. Сохраните файл.
8. Откройте файлы секрета пароля и изменения профиля, а затем внесите те же изменения, заменив `yourtenant.onmicrosoft.com` клиентом Azure AD B2C в трех местах в каждом файле. Сохраните файлы.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Добавление идентификаторов приложений в настраиваемую политику
Добавьте идентификаторы приложений в файл расширения (`TrustFrameworkExtensions.xml`).

1. В файле расширений (`TrustFrameworkExtensions.xml`) найдите элемент `<TechnicalProfile Id="login-NonInteractive">`.
2. Замените оба экземпляра `IdentityExperienceFrameworkAppId` идентификатором созданного ранее приложения инфраструктуры процедур идентификации. Пример:

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. Замените оба экземпляра `ProxyIdentityExperienceFrameworkAppId` идентификатором созданного ранее приложения прокси инфраструктуры процедур идентификации.
4. Сохраните файл расширений.

## <a name="upload-the-policies-to-your-tenant"></a>Отправка политик в клиент

1. На [портале Azure](https://portal.azure.com) [переключитесь в контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку Azure AD B2C.
2. Щелкните **Identity Experience Framework** (Инфраструктура процедур идентификации).
3. Выберите **Отправить политику**, чтобы передать файлы политики.

    >[!WARNING]
    >Файлы настраиваемой политики необходимо отправить в следующем порядке:

1. Отправьте `TrustFrameworkBase.xml`.
2. Отправьте `TrustFrameworkExtensions.xml`.
3. Отправьте `SignUpOrSignin.xml`.
4. Отправьте другие файлы политики.

При отправке к имени файла политики добавляется префикс `B2C_1A_`.

## <a name="test-the-custom-policy-using-run-now"></a>Тестирование настраиваемой политики с помощью параметра "Запустить сейчас"

1. Откройте **параметры Azure AD B2C** и перейдите к **инфраструктуре процедур идентификации**.

>[!NOTE]
>Для использования кнопки **Запустить** в клиенте должно быть зарегистрировано по крайней мере одно приложение. Это можно сделать, выбрав в клиенте B2C или инфраструктуре процедур идентификации меню **Приложения**, чтобы вызвать встроенные и настраиваемые политики. Каждое приложение необходимо зарегистрировать один раз. 

Дополнительные сведения о регистрации приложений в Azure AD B2C см. в статье [Azure Active Directory B2C: создание клиента Azure AD B2C](active-directory-b2c-get-started.md) или [Azure Active Directory B2C: регистрация приложения](active-directory-b2c-app-registration.md).  

2. Выберите отправленную вами настраиваемую политику проверяющей стороны `B2C_1A_signup_signin` и нажмите кнопку **Запустить**.


3. Вы сможете зарегистрироваться, используя адрес электронной почты.
4. Войдите в систему с помощью той же учетной записи, чтобы подтвердить правильность конфигурации.

>[!NOTE]
>Часто причина ошибки при входе заключается в неправильной настройке приложения IdentityExperienceFramework.


## <a name="next-steps"></a>Дальнейшие действия

### <a name="add-facebook-as-an-identity-provider"></a>Добавление Facebook в качестве поставщика удостоверений
Чтобы настроить Facebook, сделайте следующее:
1. [Настройте приложение Facebook на сайте developers.facebook.com.](active-directory-b2c-setup-fb-app.md)
2. [Добавьте секретный код приложения Facebook в клиент Azure AD B2C.](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)
3. Добавьте идентификатор приложения Facebook в файле политики TrustFrameworkExtensions в параметре `Item Key="client_id"`:

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. Отправьте файл политики TrustFrameworkExtensions.xml в клиент.
5. Выполните проверку с помощью кнопки **Запустить** или вызовите политику непосредственно из зарегистрированного приложения.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Добавление Azure Active Directory в качестве поставщика удостоверений
В базовом файле, использованном в этом руководстве по началу работы, уже есть некоторое содержимое, необходимое для добавления других поставщиков удостоверений. Сведения о настройке входа с использованием учетных записей Azure AD [см. здесь](active-directory-b2c-setup-aad-custom.md).


## <a name="reference"></a>Справочные материалы

Общие сведения о настраиваемых политиках Azure AD B2C с поддержкой инфраструктуры процедур идентификации см. в [этой статье](active-directory-b2c-overview-custom.md).

