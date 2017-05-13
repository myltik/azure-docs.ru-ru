---
title: "Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками | Документация Майкрософт"
description: "Инструкции по началу работы с настраиваемыми политиками Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

После выполнения действий, описанных в этой статье, настраиваемая политика будет поддерживать регистрацию и вход с помощью локальной учетной записи с использованием электронной почты и пароля. Кроме того, будет подготовлена среда для добавления дополнительных поставщиков удостоверений (например, Facebook или Azure AD).  Эти действия нужно выполнить до внедрения остальных вариантов использования подсистемы идентификации Azure AD B2C и многих основных концепций.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, убедитесь, что у вас есть клиент Azure AD B2C. Клиент Azure AD B2C — это контейнер для всех пользователей, приложений, политик и т. д. Если у вас нет клиента, его необходимо [создать](active-directory-b2c-get-started.md).

### <a name="confirming-your-b2c-tenant"></a>Подтверждение клиента B2C

Так как настраиваемые политики все еще доступны в закрытой предварительной версии, убедитесь, что ваш клиент Azure AD B2C включен для отправки настраиваемой политики.

1. На [портале Azure](https://portal.azure.com) [переключитесь в контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку Azure AD B2C.
1. Щелкните **Все политики**.
1. Проверьте, доступна ли кнопка **Отправить политику**.  Если она недоступна, введите адрес электронной почты AADB2CPreview@microsoft.com.

## <a name="set-up-keys-for-your-custom-policy"></a>Настройка ключей для настраиваемой политики

Сначала для использования настраиваемых политик нужно настроить ключи.

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>Скачивание начального пакета и изменение политик

Настраиваемые политики — это набор XML-файлов, которые нужно отправить в клиент Azure AD B2C. Мы предоставляем начальный пакет, который можно использовать для начала работы. Начальный пакет содержит:

* [Базовый файл](active-directory-b2c-overview-custom.md#policy-files) политики. Для базового файла требуется несколько изменений.
* [Файл расширения](active-directory-b2c-overview-custom.md#policy-files) политики.  В этот файл вносится большинство изменений конфигурации.

Давайте приступим.

1. Скачайте начальный пакет настраиваемой политики Azure AD B2C из репозитория GitHub.  [Скачайте ZIP-файл](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) или выполните следующую команду:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Откройте папку `SocialIDPAndLocalAccounts`.  В базовом файле (`TrustFrameworkBase.xml`) этой папки приводится содержимое, необходимое для локальных и корпоративных учетных записей или учетных записей социальной сети. Содержимое из социальных сетей не влияет на настройку и запуск локальных учетных записей.
1. Откройте `TrustFrameworkBase.xml`.  Если вам нужен редактор XML, попробуйте [скачать Visual Studio Code](https://code.visualstudio.com/download) (упрощенный кроссплатформенный редактор).
1. В корневом элементе `TrustFrameworkPolicy` обновите атрибуты `TenantId` и `PublicPolicyUri`, заменив `{tenantName}` клиентом Azure AD B2C.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. Сохраните файл.
1. Откройте `TrustFrameworkExtensions.xml` и внесите те же изменения, заменив `{tenantName}` клиентом Azure AD B2C. Сохраните файл.
1. Откройте `SignUpOrSignIn.xml` и внесите те же изменения, заменив `{tenantName}` клиентом Azure AD B2C. Сохраните файл.

>[!NOTE]
>Если в редакторе XML поддерживается проверка, можно проверить файлы, используя XML-файл схемы `TrustFrameworkPolicy_0.3.0.0.xsd` в корневой папке начального пакета. При проверке по схеме XML определяются ошибки перед отправкой.

## <a name="register-policy-engine-applications"></a>Регистрация приложений подсистемы политики

Для Azure AD B2C необходимо зарегистрировать два дополнительных приложения, которые используются подсистемой для регистрации и входа пользователей.

>[!NOTE]
>Далее мы создадим два приложения, обеспечивающие поддержку входа с помощью локальных учетных записей: PolicyEngine (веб-приложение) и PolicyEngineProxy (встроенное приложение) с делегированным разрешением PolicyEngine. Этот раздел является обязательным только для клиентов Azure AD B2C, в которых предполагается использование локальных учетных записей.

### <a name="create-the-policy-engine-application"></a>Создание приложения подсистемы политики

1. На [портале Azure](https://portal.azure.com) [переключитесь в контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Откройте колонку `Azure Active Directory` (а не колонку Azure AD B2C). Чтобы ее найти, щелкните **> More Services** (> Больше служб).
1. Щелкните **Регистрация приложений**.
1. Щелкните **+ New application registration** (+ Регистрация нового приложения).
   * Имя: `PolicyEngine`.
   * Тип приложения: `Web app/API`.
   * URL-адрес входа: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`, где `{tenantName}` — это клиент Azure AD B2C.
1. Щелкните **Создать**.
1. После этого выберите созданное приложение `PolicyEngine`, скопируйте идентификатор приложения и сохраните его для последующего использования.

### <a name="create-the-policy-engine-proxy-application"></a>Создание прокси приложения подсистемы политики

1. Щелкните **Регистрация приложений**.
1. Щелкните **+ New application registration** (+ Регистрация нового приложения).
   * Имя: `PolicyEngineProxy`.
   * Тип приложения: `Native`.
   * URL-адрес входа: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`, где `{tenantName}` — это клиент Azure AD B2C.
1. Щелкните **Создать**.
1. После создания выберите приложение `PolicyEngineProxy`, скопируйте идентификатор приложения и сохраните его для последующего использования.
1. Выберите **Необходимые разрешения**, щелкните **+ Add** (+ Добавить), а затем **Выбор API**.
1. Найдите имя `PolicyEngine`, выберите PolicyEngine в результатах и щелкните **Выбрать**.
1. Установите флажок рядом с `Access PolicyEngine`, а затем щелкните **Выбрать**.
1. Нажмите кнопку **Done**(Готово).

### <a name="add-the-application-ids-to-your-custom-policy"></a>Добавление идентификаторов приложений в настраиваемую политику

Чтобы создать настраиваемую политику с включенными локальными учетными записями, необходимо добавить идентификаторы приложений в файл расширений (`TrustFrameworkExtensions.xml`).

1. В файле расширений (`TrustFrameworkExtensions.xml`) найдите элемент `<TechnicalProfile Id="login-NonInteractive">`.
1. Замените оба экземпляра `{Policy Engine Proxy Application ID}` идентификатором [созданного прокси приложения подсистемы политики](#create-the-policy-engine-proxy-application).
1. Замените оба экземпляра `{Policy Engine Application ID}` идентификатором [созданного приложения подсистемы политики](#create-the-policy-engine-application).
1. Сохраните файл расширений.

## <a name="upload-the-policies-to-your-tenant"></a>Отправка политик в клиент

1. На [портале Azure](https://portal.azure.com) [переключитесь в контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку Azure AD B2C.
1. Щелкните **Все политики**.
1. Щелкните **Отправить политику**.

    >[!WARNING]
    >Файлы настраиваемой политики необходимо отправить в следующем порядке:

1. Отправьте `TrustFrameworkBase.xml`.
1. Отправьте `TrustFrameworkExtensions.xml`.
1. Отправьте `SignUpOrSignin.xml`.

При отправке файла указывается имя с префиксом `B2C_1A_`,  а встроенные политики начинаются с `B2C_1_`.

## <a name="test-the-custom-policy-using-run-now"></a>Тестирование настраиваемой политики с помощью параметра "Запустить сейчас"

1. Откройте **колонку Azure AD B2C** и щелкните **Все политики**.
1. Выберите отправленную вами настраиваемую политику и нажмите кнопку **Запустить сейчас**.
1. Вы сможете зарегистрироваться, используя адрес электронной почты.

## <a name="next-steps"></a>Дальнейшие действия

В базовом файле, использованном в этом руководстве по началу работы, уже есть некоторое содержимое, необходимое для добавления других поставщиков удостоверений. Сведения о настройке входа с использованием учетных записей Azure AD [см. здесь](active-directory-b2c-setup-aad-custom.md).

## <a name="reference"></a>Справочные материалы

* **Технический профиль (TP)** — это элемент, определяющий имя конечной точки, ее метаданные, протокол, а также предоставляющий сведения об обмене утверждениями, который должна выполнять подсистема идентификации.  Локальная учетная запись для входа является техническим профилем, используемым подсистемой идентификации для выполнения входа.

