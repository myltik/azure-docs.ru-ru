---
title: 'Azure Active Directory B2C: встроенные политики | Документы Майкрософт'
description: В разделе описывается инфраструктура расширяемых политик Azure Active Directory B2C и создание различных типов политик.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/26/2017
ms.author: davidmu
ms.openlocfilehash: 424186a0acfe17cd7cb96f3ba7f8201e8b2b38ec
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200354"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: встроенные политики


Инфраструктура расширяемых политик Azure Active Directory (Azure AD) B2C — это основное преимущество службы. Политики полностью описывают процесс идентификации пользователей: регистрацию, вход и редактирование профиля. К примеру, политика регистрации позволяет управлять поведением с помощью таких параметров:

* типы учетных записей (учетные записи в социальных сетях, например Facebook, или локальные учетные записи, например адреса электронной почты), которые пользователи могут применять для регистрации в приложении;
* атрибуты (например, имя, почтовый индекс и размер обуви), которые нужно получить от пользователя во время регистрации;
* использование Многофакторной идентификации Azure;
* внешний вид и удобство использования всех страниц регистрации;
* сведения (объявляемые в качестве утверждений в токене), которые получает приложение после завершения выполнения политики.

В своем клиенте вы можете создать несколько политик различных типов и при необходимости использовать их в приложениях. Политики могут повторно использоваться в разных приложениях. Такая гибкость позволяет разработчикам определять и изменять процесс идентификации пользователя, внося лишь незначительные изменения в код или совсем не изменяя его.

Работа с политиками выполняется через простой интерфейс разработчика. Приложение запускает политику с помощью стандартного HTTP-запроса на проверку подлинности (передавая в запросе параметр политики) и в ответ получает настроенный токен. Например, единственное различие между запросами, которые вызывают политику регистрации и политику входа, заключается в имени политики, которое используется в качестве строкового параметра "p" в запросе.

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Создание политики регистрации или входа в систему

Эта политика регулирует процедуры регистрации и входа в систему в рамках одной конфигурации. Потребители проводятся через соответствующую процедуру (регистрации или входа в систему) в зависимости от контекста. В ней также описывается содержимое токенов, которые приложение будет получать при успешной регистрации или входе в систему.  Пример кода для политики **регистрации или входа** см. [здесь](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Мы рекомендуем использовать эту политику вместо политики **регистрации** или политики **входа**.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Создание политики регистрации

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Создание политики входа в систему

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Создание политики изменения профиля

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Создание политики сброса паролей

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Политики предварительной версии

С выпуском новых функций некоторые из них могут быть недоступны в имеющихся политиках.  Мы планируем заменить старые версии на последнюю версию того же типа, как только появится общедоступная версия этих политик.  Имеющиеся политики не изменятся. Чтобы воспользоваться преимуществами новых функций, необходимо создать политики.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Как связать политику регистрации или входа с политикой сброса паролей?
При создании политики **регистрации или входа** (с помощью локальных учетных записей) на первой странице отображается ссылка **Забыли пароль?**. При переходе по этой ссылке политика сброса паролей не активируется автоматически. 

Вместо этого в приложение возвращается код ошибки **`AADB2C90118`**. Приложение должно обработать этот код, вызвав определенную политику сброса паролей. Дополнительные сведения см. в [примере, демонстрирующем подход к связыванию политик](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Следует ли использовать политику регистрации или входа либо две политики: политику регистрации и политику входа?
Мы рекомендуем использовать политику **регистрации или входа** вместо политики **регистрации** и политики **входа**.  

Политика **регистрации или входа** обеспечивает больше возможностей, чем политика **входа**. Она также позволяет настраивать пользовательский интерфейс страницы и лучше поддерживает локализацию. 

Политика **входа** рекомендуется, если вам не нужно локализовать политики либо если вам требуются ограниченные возможности для настройки фирменного оформления и встроенные возможности сброса паролей.

## <a name="next-steps"></a>Дополнительная информация
* [Настройка токена, сеанса и единого входа](active-directory-b2c-token-session-sso.md)
* [Azure Active Directory B2C: отключение проверки адреса электронной почты во время регистрации потребителя](active-directory-b2c-reference-disable-ev.md)

