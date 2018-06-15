---
title: Руководство. Предоставление доступа к веб-API Node.js из классического приложения с помощью Azure Active Directory B2C | Документация Майкрософт
description: Руководство по использованию Active Directory B2C для защиты веб-API Node.js и его вызова из классического приложения NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 01c13b214d40fba278ce788047e2b158adc20287
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711602"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Руководство. Предоставление доступа к веб-API Node.js из классического приложения с помощью Azure Active Directory B2C

В этом руководстве показано, как вызывать защищенный ресурс веб-API Node.js Azure Active Directory (Azure AD) B2C из классического приложения Windows Presentation Foundation (WPF).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Регистрация веб-API в клиенте Azure AD B2C.
> * Определение и настройка областей для веб-API.
> * Предоставление приложению прав для использования веб-API.
> * Обновление примера кода для защиты веб-API с помощью Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

* Изучите руководство [Включение в классическом приложении аутентификации на основе учетных записей с помощью Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).
* [Установите Visual Studio 2017](https://www.visualstudio.com/downloads/), а также рабочие нагрузки **Разработка классических приложений .NET** и **ASP.NET и разработка веб-приложений**.
* Установите [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Регистрация веб-API

Ресурсы веб-API необходимо зарегистрировать в клиенте, чтобы они могли принимать [запросы защищенных ресурсов](../active-directory/develop/active-directory-dev-glossary.md#resource-server) от [клиентских приложений](../active-directory/develop/active-directory-dev-glossary.md#client-application), которые представляют [токен доступа](../active-directory/develop/active-directory-dev-glossary.md#access-token) из Azure Active Directory, и отвечать на них. Регистрация устанавливает [приложение и объект субъекта-службы](../active-directory/develop/active-directory-dev-glossary.md#application-object) в клиенте. 

Войдите на [портал Azure](https://portal.azure.com/) как глобальный администратор клиента Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Выберите **Azure AD B2C** из списка служб на портале Azure.

2. В разделе параметров B2C щелкните **Приложения**, а затем — **Добавить**.

    Чтобы зарегистрировать пример веб-API в клиенте, используйте следующие параметры.
    
    ![Добавление нового API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Имя** | My sample Node.js web API (Пример веб-API Node.js) | Введите **имя**, которое описывает веб-API для разработчиков. |
    | **Включить веб-приложение или веб-интерфейс API** | Yes | Выберите **Да** для веб-API. |
    | **Разрешить неявный поток** | Yes | Выберите **Да**, так как API использует [вход в OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL-адрес ответа** | `http://localhost:5000` | URL-адреса ответа — это конечные точки, куда Azure AD B2C возвращает все токены, запрашиваемые вашим API. В этом руководстве пример веб-API выполняется локально (localhost) и ожидает передачи данных через порт 5000. |
    | **URI кода приложения** | demoapi | URI уникально идентифицирует API в клиенте. Это позволяет регистрировать несколько API-интерфейсов в каждом клиенте. [Области](../active-directory/develop/active-directory-dev-glossary.md#scopes) управляют доступом к защищенному ресурсу API и определяются для каждого URI идентификатора приложения. |
    | **Собственный клиент** | Нет  | Так как это веб-API, а не собственный клиент, выберите "Нет". |
    
3. Чтобы зарегистрировать API, щелкните **Создать**.

Зарегистрированные API отобразятся в списке приложений для клиента Azure AD B2C. Выберите веб-API в списке. Откроется панель свойств веб-API.

![Свойства веб-API](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Запишите значение **идентификатора клиента приложения**. Идентификатор уникально идентифицирует API и необходим при настройке API далее в этом руководстве.

Регистрация веб-API в Azure AD B2C позволяет определить отношение доверия. Так как API зарегистрирован в B2C, он теперь может доверять токенам доступа B2C, предоставляемым другими приложениями.

## <a name="define-and-configure-scopes"></a>Определение и настройка областей

[Области](../active-directory/develop/active-directory-dev-glossary.md#scopes) предоставляют способ контроля доступа к защищенным ресурсам. Области используются веб-API для реализации управления доступом на уровне области. Например, некоторые пользователи могут иметь доступ на чтение и запись, тогда как другие пользователи могут иметь разрешения только на чтение. В этом руководстве вы определяете разрешения на чтение и запись для веб-API.

### <a name="define-scopes-for-the-web-api"></a>Определение областей для веб-API

Зарегистрированные API отобразятся в списке приложений для клиента Azure AD B2C. Выберите веб-API в списке. Откроется панель свойств веб-API.

Щелкните **Опубликованные области (предварительная версия)**.

Чтобы настроить области для API, добавьте следующие записи. 

![области, определенные в веб-API](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Область** | demo.read | Доступ на чтение к демонстрационному API|

Выберите команду **Сохранить**.

Опубликованные области можно использовать для предоставления приложению клиента разрешения на доступ к веб-API.

### <a name="grant-app-permissions-to-web-api"></a>Предоставление приложению разрешений на использование веб-API

Чтобы вызвать защищенный веб-API из приложения, необходимо предоставить приложению разрешения на доступ к API. В этой статье используется классическое приложение, созданное в рамках работы с [руководством по проверке подлинности пользователей с помощью Azure Active Directory B2C в классическом приложении](active-directory-b2c-tutorials-desktop-app.md).

1. На портале Azure выберите **Azure AD B2C** из списка служб и щелкните **Приложения**, чтобы просмотреть список зарегистрированных приложений.

2. Выберите **My Sample WPF App** (Пример приложения WPF) в списке приложений и щелкните **Доступ через API (предварительная версия)**, а затем — **Добавить**.

3. В раскрывающемся списке **Выбрать API** выберите зарегистрированный веб-API **My sample Node.js web API**.

4. В раскрывающемся списке **Выбрать области** выберите области, определенные в регистрации веб-API.

    ![выбор областей для приложения](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Последовательно выберите **ОК**.

Приложение **My Sample WPF App** зарегистрировано для вызова защищенного веб-API **My sample Node.js web API**. Пользователь [выполняет аутентификацию](../active-directory/develop/active-directory-dev-glossary.md#authentication) с помощью Azure AD B2C для использования классического приложения WPF. Классическое приложение получает [предоставление авторизации](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) из Azure AD B2C для доступа к защищенному веб-API.

## <a name="update-web-api-code"></a>Обновление кода веб-API

Теперь, когда веб-API зарегистрирован и определены области, необходимо настроить код веб-API для использования клиента Azure AD B2C. С помощью этого руководства вы настроите пример веб-приложения Node.js, который можно скачать из репозитория GitHub. 

[Загрузите ZIP-файл](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) или клонируйте пример приложения с GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Пример веб-API Node.js с помощью библиотеки Passport.js активирует Azure AD B2C для защиты вызовов API. 

### <a name="configure-the-web-api"></a>Настройка веб-API

1. Откройте файл `index.html` в примере веб-API Node.js.
2. Настройте пример, указав сведения о регистрации клиента Azure AD B2C. Измените следующие строки кода:

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Настройка классического приложения

1. Откройте решение `active-directory-b2c-wpf`, описанное в руководстве [Включение в классическом приложении аутентификации на основе учетных записей с помощью Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md), в Visual Studio.

## <a name="run-the-sample"></a>Запуск примера

Запустите веб-API Node.js:

1. Откройте командную строку Node.js.
2. Перейдите в каталог, содержащий пример приложения Node.js. Например `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Выполните следующие команды:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
Запустите классическое приложение:

1. Нажмите клавишу **F5**, чтобы запустить классическое приложение.
2. Войдите с помощью адреса электронной почты и пароля, которые использовались при работе с [руководством по проверке подлинности пользователей с помощью Azure Active Directory B2C в классическом приложении](active-directory-b2c-tutorials-desktop-app.md).
3. Нажмите кнопку **вызова API**. 

Классическое приложение отправляет запрос к веб-API и получает ответ, содержащий отображаемое имя пользователя, вошедшего в систему. Защищенное классическое приложение вызывает защищенный веб-API в клиенте Azure AD B2C.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете использовать ваш клиент Azure AD B2C при работе с другими руководствами по Azure AD B2C. [Удалите клиент Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), если он больше не нужен.

## <a name="next-steps"></a>Дополнительная информация

В этой статье подробно описан процесс защиты веб-приложения ASP.NET путем регистрации и определения областей в Azure AD B2C. Чтобы узнать больше, просмотрите доступные примеры кода Azure AD B2C.

> [!div class="nextstepaction"]
> [Примеры кода Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
