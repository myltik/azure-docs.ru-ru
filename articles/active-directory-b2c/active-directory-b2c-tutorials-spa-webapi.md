---
title: Руководство. Предоставление доступа к веб-API ASP.NET Core из одностраничного приложения с помощью Azure Active Directory B2C | Документация Майкрософт
description: Руководство по использованию Active Directory B2C для защиты веб-API .NET Core и его вызова из одностраничного приложения.
services: active-directory-b2c
author: davidmu1
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: b1731a16dd2389f19d25a2594fe25a97b6ddd9f1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Руководство. Предоставление доступа к веб-API ASP.NET Core из одностраничного приложения с помощью Azure Active Directory B2C

В этом руководстве показано, как вызывать защищенный с помощью Azure Active Directory (Azure AD) B2C ресурс веб-API ASP.NET Core из одностраничного приложения.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Регистрация веб-API в клиенте Azure AD B2C.
> * Определение и настройка областей для веб-API.
> * Предоставление приложению прав для использования веб-API.
> * Обновление примера кода для защиты веб-API с помощью Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Изучите [руководство по проверке подлинности пользователей с помощью Azure Active Directory B2C в одностраничном приложении](active-directory-b2c-tutorials-spa.md).
* Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/) с рабочей нагрузкой **ASP.NET и веб-разработка**.
* Установите [пакет SDK для NET Core 2.0.0](https://www.microsoft.com/net/core) или более поздней версии.
* Установите [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Регистрация веб-API

Ресурсы веб-API необходимо зарегистрировать в клиенте, чтобы они могли принимать [запросы защищенных ресурсов](../active-directory/develop/active-directory-dev-glossary.md#resource-server) от [клиентских приложений](../active-directory/develop/active-directory-dev-glossary.md#client-application), которые представляют [токен доступа](../active-directory/develop/active-directory-dev-glossary.md#access-token) из Azure Active Directory, и отвечать на них. Регистрация устанавливает [приложение и объект субъекта-службы](../active-directory/develop/active-directory-dev-glossary.md#application-object) в клиенте. 

Войдите на [портал Azure](https://portal.azure.com/) как глобальный администратор клиента Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Выберите **Azure AD B2C** из списка служб на портале Azure.

2. В разделе параметров B2C щелкните **Приложения**, а затем — **Добавить**.

    Чтобы зарегистрировать пример веб-API в клиенте, используйте следующие параметры.
    
    ![Добавление нового API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Параметр      | Рекомендуемое значение  | Описание                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Имя** | Hello Core API | Введите **имя**, которое описывает веб-API для разработчиков. |
    | **Включить веб-приложение или веб-интерфейс API** | Yes | Выберите **Да** для веб-API. |
    | **Разрешить неявный поток** | Yes | Выберите **Да**, так как API использует [вход в OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL-адрес ответа** | `http://localhost:44332` | URL-адреса ответа — это конечные точки, куда Azure AD B2C возвращает все токены, запрашиваемые вашим API. В этом руководстве пример веб-API выполняется локально (localhost) и ожидает передачи данных через порт 5000. |
    | **URI кода приложения** | HelloCoreAPI | URI уникально идентифицирует API в клиенте. Это позволяет регистрировать несколько API-интерфейсов в каждом клиенте. [Области](../active-directory/develop/active-directory-dev-glossary.md#scopes) управляют доступом к защищенному ресурсу API и определяются для каждого URI идентификатора приложения. |
    | **Собственный клиент** | Нет  | Так как это веб-API, а не собственный клиент, выберите "Нет". |
    
3. Чтобы зарегистрировать API, щелкните **Создать**.

Зарегистрированные API отобразятся в списке приложений для клиента Azure AD B2C. Выберите веб-API в списке. Откроется панель свойств веб-API.

![Свойства веб-API](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Запишите значение **идентификатора клиента приложения**. Идентификатор уникально идентифицирует API и необходим при настройке API далее в этом руководстве.

Регистрация веб-API в Azure AD B2C позволяет определить отношение доверия. Так как API зарегистрирован в B2C, он теперь может доверять токенам доступа B2C, предоставляемым другими приложениями.

## <a name="define-and-configure-scopes"></a>Определение и настройка областей

[Области](../active-directory/develop/active-directory-dev-glossary.md#scopes) предоставляют способ контроля доступа к защищенным ресурсам. Области используются веб-API для реализации управления доступом на уровне области. Например, некоторые пользователи могут иметь доступ на чтение и запись, тогда как другие пользователи могут иметь разрешения только на чтение. В этом руководстве вы определяете разрешения на чтение для веб-API.

### <a name="define-scopes-for-the-web-api"></a>Определение областей для веб-API

Зарегистрированные API отобразятся в списке приложений для клиента Azure AD B2C. Выберите веб-API в списке. Откроется панель свойств веб-API.

Щелкните **Опубликованные области (предварительная версия)**.

Чтобы настроить области для API, добавьте следующие записи. 

![области, определенные в веб-API](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Область** | demo.read | Доступ на чтение к демонстрационному API |

Выберите команду **Сохранить**.

Опубликованные области можно использовать для предоставления приложению клиента разрешения на доступ к веб-API.

### <a name="grant-app-permissions-to-web-api"></a>Предоставление приложению разрешений на использование веб-API

Чтобы вызвать защищенный веб-API из приложения, необходимо предоставить приложению разрешения на доступ к API. В этой статье используется одностраничное приложение, созданное в рамках работы с руководством по [проверке подлинности пользователей с помощью Azure Active Directory B2C в одностраничном приложении (JavaScript)](active-directory-b2c-tutorials-spa.md).

1. На портале Azure выберите **Azure AD B2C** из списка служб и щелкните **Приложения**, чтобы просмотреть список зарегистрированных приложений.

2. Выберите **My sample single page app** (Пример одностраничного приложения) в списке приложений и щелкните **Доступ через API (предварительная версия)**, а затем — **Добавить**.

3. В раскрывающемся списке **Выбрать API** выберите зарегистрированный веб-API **Hello Core API**.

4. В раскрывающемся списке **Выбрать области** выберите области, определенные в регистрации веб-API.

    ![выбор областей для приложения](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Последовательно выберите **ОК**.

Приложение **My sample single page app** зарегистрировано для вызова защищенного **Hello Core API**. Пользователь [выполняет аутентификацию](../active-directory/develop/active-directory-dev-glossary.md#authentication) с помощью Azure AD B2C для использования классического приложения WPF. Классическое приложение получает [предоставление авторизации](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) из Azure AD B2C для доступа к защищенному веб-API.

## <a name="update-code"></a>Обновление кода

Теперь, когда веб-API зарегистрирован и определены области, необходимо настроить код веб-API для использования клиента Azure AD B2C. С помощью этого руководства вы настроите пример веб-приложения .NET Core, который можно скачать из репозитория GitHub. 

[Загрузите ZIP-файл](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) или клонируйте пример приложения с GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Настройка веб-API

1. Откройте решение **B2C-WebAPI.sln** в Visual Studio.

2. Откройте файл **appsettings.json**. Обновите следующие значения, чтобы настроить веб-API для использования со своим клиентом:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>Включение CORS

Чтобы разрешить одностраничному приложению вызывать веб-API ASP.NET Core, необходимо включить [CORS](https://docs.microsoft.com/en-us/aspnet/core/security/cors).

1. В файле **Startup.cs** добавьте CORS в метод `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. В файле **Startup.cs** настройте CORS в методе `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

### <a name="configure-the-single-page-app"></a>Настройка одностраничного приложения

Одностраничное приложение использует Azure AD B2C для регистрации и входа пользователей, а также вызывает защищенный веб-API ASP.NET Core. Для вызова веб-API .NET Core в одностраничное приложение необходимо внести изменения.
Чтобы изменить параметры приложения:

1. Откройте файл `index.html` в примере одностраничного приложения Node.js.
2. Настройте пример, указав сведения о регистрации клиента Azure AD B2C. Измените значения **b2cScopes** и **webApi** в следующих строках кода:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:58553/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Запуск одностраничного приложения и веб-API

Теперь нужно запустить одностраничное приложение Node.js и веб-API .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Запуск веб-API ASP.NET Core 

Нажмите клавишу **F5** для отладки решения **B2C-WebAPI.sln** в Visual Studio.

При запуске проекта в браузере, используемом по умолчанию, откроется веб-страница с объявлением о том, что веб-API доступен для запросов.

### <a name="run-the-single-page-app"></a>Запуск одностраничного приложения

1. Откройте командную строку Node.js.
2. Перейдите в каталог, содержащий пример приложения Node.js. Например `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Выполните следующие команды:
    ```
    npm install && npm update
    node server.js
    ```

    В окне консоли отобразится номер порта, связанный с размещаемым приложением.
    
    ```
    Listening on port 6420...
    ```

4. С помощью браузера перейдите по адресу `http://localhost:6420`, чтобы просмотреть приложение.
5. Войдите с помощью адреса электронной почты и пароля, которые использовались при работе с руководством по [проверке подлинности пользователей с помощью Azure Active Directory B2C в одностраничном приложении (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Нажмите кнопку **вызова API**.

После регистрации или входа с учетной записью пользователя пример приложения вызовет защищенный веб-API и вернет результат.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете использовать ваш клиент Azure AD B2C при работе с другими руководствами по Azure AD B2C. [Удалите клиент Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), если он больше не нужен.

## <a name="next-steps"></a>Дополнительная информация

В этой статье подробно описан процесс защиты веб-приложения путем регистрации и определения областей в Azure AD B2C. Чтобы узнать больше, просмотрите доступные примеры кода Azure AD B2C.

> [!div class="nextstepaction"]
> [Примеры кода Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)