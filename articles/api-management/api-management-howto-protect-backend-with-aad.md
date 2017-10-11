---
title: "Защита серверной части веб-API с помощью Azure Active Directory и службы управления API | Документация Майкрософт"
description: "Информация о защите внутренней службы веб-API с помощью Azure Active Directory и управления API"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: f856ff03-64a1-4548-9ec4-c0ec4cc1600f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 0dfb4102904c2e972e6617fd3851fb1c50147357
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Защита внутренней службы веб-API с помощью Azure Active Directory и управления API
На следующих видео показано, как собрать внутреннюю службу веб-API и защитить ее, используя протокол OAuth 2.0 с Azure Active Directory и управлением API.  Эта статья содержит обзор инструкций на видео и дополнительные сведения к ним. Посмотрев этот 24-минутный ролик, вы познакомитесь со следующими темами.

* Создание серверной части веб-API и обеспечение ее безопасности при помощи AAD ― с 1:30
* Импорт API в управление API ― с 7:10
* Настройка портала разработчика на вызов API ― с 9:09
* Настройка классического приложения для вызова API ― с 18:08
* Настройка политики проверки JWT для предварительной авторизации запросов ― с 20:47

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>Создание каталога Azure AD
Для защиты внутренней службы веб-API с помощью Azure Active Directory сначала необходимо установить клиент AAD. В этом видеоролике используется клиент с именем **APIMDemo** . Чтобы создать клиент AAD, войдите на [классический портал Azure](https://manage.windowsazure.com) и нажмите кнопку **Создать**->**Службы приложений**->**Active Directory**->**Каталог**->**Настраиваемое создание**. 

![Azure Active Directory][api-management-create-aad-menu]

В этом примере каталог с именем **APIMDemo** создается в домене по умолчанию с именем **DemoAPIM.onmicrosoft.com**. Этот каталог используется в видео.

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Создание службы веб-API, защищенной с помощью Azure Active Directory
На этом шаге создается внутренняя служба веб-API с помощью Visual Studio 2013. Этот шаг начинается на видео с отметки времени 1:30. Для создания проекта внутренней службы веб-API в Visual Studio выберите **Файл**->**Создать**->**Проект** и в списке **веб**-шаблонов выберите **Веб-приложение ASP.NET**. В этом видео проекту присвоено имя **APIMAADDemo**. Нажмите кнопку **ОК** , чтобы создать проект. 

![Visual Studio][api-management-new-web-app]

Чтобы создать проект веб-API, в списке **Выбор шаблона** выберите **Веб-API**. Чтобы настроить проверку подлинности Azure Active Directory, щелкните **Изменить проверку подлинности**.

![Новый проект][api-management-new-project]

Выберите **Учетные записи организации** и укажите **домен** клиента AAD. В этом примере используется домен **DemoAPIM.onmicrosoft.com**. Домен каталога можно найти на вкладке **Домены** вашего каталога.

![Домены][api-management-aad-domains]

Настройте нужные параметры в диалоговом окне **Изменить способ проверки подлинности** и нажмите кнопку **ОК**.

![Изменить проверку подлинности][api-management-change-authentication]

После того как вы нажмете кнопку **ОК** , Visual Studio попытается зарегистрировать приложение с каталогом Azure AD, и вам будет предложено войти в Visual Studio. Войдите с помощью учетной записи администратора вашего каталога.

![Вход в Visual Studio][api-management-sign-in-vidual-studio]

Чтобы настроить этот проект как веб-API Azure, установите флажок рядом с параметром **Разместить в облаке** и нажмите кнопку **ОК**.

![Новый проект][api-management-new-project-cloud]

Может появиться запрос на вход в Azure, после входа вы сможете настроить веб-приложение.

![Настройка][api-management-configure-web-app]

В этом примере создается новый **план службы приложений** с именем **APIMAADDemo**.

Нажмите кнопку **ОК** , чтобы настроить веб-приложение и создать проект.

## <a name="add-the-code-to-the-web-api-project"></a>Добавление кода в проект веб-API
На следующем шаге в видео добавляется код в проект веб-API. Этот шаг начинается с отметки времени 4:35.

Веб-API в этом примере реализует простую службу «Калькулятор» с помощью модели и контроллера. Чтобы добавить модель в службу, щелкните правой кнопкой мыши **Модели** в **обозревателе решений** и выберите **Добавить**, **Класс**. Присвойте классу имя `CalcInput` и нажмите **Добавить**.

Добавьте следующий оператор `using` в верхнюю часть файла `CalcInput.cs`:

```c#
using Newtonsoft.Json;
```

Замените созданный класс следующим кодом:

```c#
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Щелкните правой кнопкой мыши **Контроллеры** в **обозревателе решений** и выберите **Добавить**->**Контроллер**. Выберите элемент **Контроллер Web API 2 – пустой** и нажмите кнопку **Добавить**. Введите имя контроллера **CalcController** и нажмите кнопку **Добавить**.

![Добавление контролера][api-management-add-controller]

Добавьте следующий оператор `using` в верхнюю часть файла `CalcController.cs`:

```c#
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Замените созданный контроллер следующим кодом: Этот код реализует операции `Add`, `Subtract`, `Multiply` и `Divide` простого API «Калькулятор».

```c#
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Нажмите клавишу **F6** , чтобы построить и проверить решение.

## <a name="publish-the-project-to-azure"></a>Публикация проекта в Azure
На этом этапе проект Visual Studio публикуется в Azure. Этот шаг начинается на видео с отметки времени 5:45.

Чтобы опубликовать проект в Azure, щелкните правой кнопкой мыши проект **APIMAADDemo** в Visual Studio и выберите **Опубликовать**. Сохраните параметры по умолчанию в диалоговом окне **веб-публикации** и нажмите кнопку **Опубликовать**.

![Веб-публикация][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Предоставление разрешений для приложения внутренней службы Azure AD
Новое приложение для внутренней службы создается в каталоге Azure AD как часть процесса настройки и публикации проекта веб-API. На этом шаге видео, который начинается с отметки времени 6:13, разрешения предоставляются для внутренней службы веб-API.

![Приложение][api-management-aad-backend-app]

Щелкните имя приложения, чтобы настроить необходимые разрешения. На вкладке **Настройка** прокрутите страницу вниз до раздела **Разрешения для других приложений**. Щелкните раскрывающийся список **Разрешения приложений** рядом с **Microsoft** **Azure Active Directory**, установите флажок рядом с разрешением **Чтение данных каталога** и нажмите кнопку **Сохранить**.

![Добавление разрешений][api-management-aad-add-permissions]

> [!NOTE]
> Если приложение **Microsoft** **Azure Active Directory** не указано в списке разрешений для других приложений, нажмите кнопку **Добавить приложение** и добавьте его из списка.
> 
> 

Запишите **URI идентификатора приложения** , чтобы использовать его на следующем шаге во время настройки приложения Azure AD для портала разработчика управления API.

![URI идентификатора приложения][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Импорт веб-API в управление API
Интерфейсы API настраиваются на портале издателя API, который доступен на портале Azure. Чтобы открыть его, щелкните **Publisher portal** (Портал издателя) на панели инструментов своей службы управления API. Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Create an API Management service instance] в руководстве [Начало работы со службой управления Azure API][Manage your first API].

![Портал издателя][api-management-management-console]

Операции можно [добавить в API-интерфейсы вручную](api-management-howto-add-operations.md)или импортировать их. В этом видео операции импортируются в формате Swagger начиная с отметки времени 6:40.

Создайте файл `calcapi.json` с приведенным ниже содержимым и сохраните его на компьютер. Убедитесь, что атрибут `host` указывает на серверную часть веб-API. В этом примере используется `"host": "apimaaddemo.azurewebsites.net"` .

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Чтобы импортировать API калькулятора, щелкните **API** в расположенном слева меню **Управление API**, а затем выберите **Импорт API**.

![Кнопка импорта API][api-management-import-api]

Выполните следующие действия, чтобы настроить API калькулятора.

1. Щелкните **Из файла**, перейдите к сохраненному файлу `calculator.json` и установите переключатель **Swagger**.
2. В текстовом поле **Web API URL suffix** (Суффикс URL-адреса веб-API) введите **calc**.
3. Щелкните в поле **Products (optional)** (Продукты (необязательно)) и выберите **Starter**.
4. Щелкните **Сохранить** , чтобы импортировать API.

![Добавление нового API][api-management-import-new-api]

После импорта API на портале издателя выводится страница сводных данных для API.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Неудавшийся вызов API с портала разработчика
На этом этапе API импортирован в управление API, но его еще нельзя успешно вызвать с портала разработчика, поскольку внутренняя служба защищена с помощью проверки подлинности Azure AD. Это продемонстрировано на видео на отметке времени 7:40 следующим образом.

Выберите **Портал разработчика** справа вверху на портале издателя.

![Портал разработчика][api-management-developer-portal-menu]

Щелкните **Интерфейсы API** и выберите API **Калькулятор**.

![Портал разработчика][api-management-dev-portal-apis]

Щелкните **Попробовать**.

![Попробовать][api-management-dev-portal-try-it]

Нажмите кнопку **Отправить**. Состояние ответа должно быть **401 — Не санкционировано**.

![Отправка][api-management-dev-portal-send-401]

Запрос не авторизован, поскольку внутренняя служба API защищена службой Azure Active Directory. Для успешного вызова API портал разработчика необходимо настроить, чтобы авторизовать разработчиков, использующих OAuth 2.0. Этот процесс описывается в следующих разделах.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Регистрация портала разработчика как приложения AAD
Чтобы настроить портал разработчика для авторизации разработчиков, использующих OAuth 2.0, сначала необходимо зарегистрировать портал разработчика как приложение AAD. На видео этот процесс показан с отметки времени 8:27.

Перейдите к клиенту Azure AD, который использовался в первом шаге этого видео (в нашем примере это клиент **APIMDemo**), и откройте вкладку **Приложения**.

![Новое приложение][api-management-aad-new-application-devportal]

Нажмите кнопку **Добавить**, чтобы создать приложение Azure Active Directory, а затем установите флажок **Добавить приложение, разрабатываемое моей организацией**.

![Новое приложение][api-management-new-aad-application-menu]

Выберите **Веб-приложение и/или веб-API**, введите имя и нажмите стрелку «Далее». В этом примере используется **APIMDeveloperPortal** .

![Новое приложение][api-management-aad-new-application-devportal-1]

В поле **URL-адрес входа** введите URL-адрес службы управления API и добавьте `/signin`. В этом примере используется `https://contoso5.portal.azure-api.net/signin` .

В поле **App Id URL** (URL-адрес идентификатора приложения) введите URL-адрес службы управления API и добавьте несколько уникальных символов. Это могут быть любые знаки. В данном примере используется `https://contoso5.portal.azure-api.net/dp`. Настроив нужные **свойства приложения**, установите флажок для создания приложения.

![Новое приложение][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Настройка сервера авторизации OAuth 2.0 в управлении API
На следующем шаге будет настроен сервер авторизации OAuth 2.0 в управлении API Этот шаг показан в видео начиная с отметки времени 9:43.

В меню "Управление API" слева выберите пункт **Безопасность**, перейдите на вкладку **OAuth 2.0** и щелкните ссылку **Add authorization server** (Добавить сервер авторизации).

![Добавление сервера авторизации][api-management-add-authorization-server]

В полях **Имя** и **Описание** введите имя и (при желании) описание. Эти поля служат для идентификации сервера авторизации OAuth 2.0 в текущем экземпляре службы управления API. В этом примере используется **демоверсия сервера авторизации** . Позже, когда вы укажете сервер OAuth 2.0 проверки подлинности для API, вам нужно будет выбрать это имя.

В поле **Client registration page URL** (URL-адрес страницы регистрации клиента) введите значение заполнителя, например `http://localhost`.  **URL-адрес страницы регистрации клиента** указывает на страницу, на которой пользователи могут создавать и настраивать собственные учетные записи для поставщиков OAuth 2.0, поддерживающих пользовательское управление учетными записями. В этом примере пользователи не создают и не настраивают собственные учетные записи, поэтому используется заполнитель.

![Добавление сервера авторизации][api-management-add-authorization-server-1]

Затем укажите **URL-адрес конечной точки авторизации** и **URL-адрес конечной точки маркера**.

![Сервер авторизации][api-management-add-authorization-server-1a]

Эти значения можно получить на странице **Конечные точки приложения** для приложения AAD, которое вы создали для портала разработчика. Для доступа к конечным точкам откройте вкладку **Настройка** приложения AAD и нажмите кнопку **Просмотр конечных точек**.

![Приложение][api-management-aad-devportal-application]

![Просмотреть конечные точки][api-management-aad-view-endpoints]

Скопируйте значение **Конечная точка авторизации OAuth 2.0** и вставьте его в текстовое поле **Authorization endpoint URL** (URL-адрес конечной точки авторизации).

![Добавление сервера авторизации][api-management-add-authorization-server-2]

Скопируйте значение **Конечная точка маркера OAuth 2.0** и вставьте его в текстовое поле **Token endpoint URL** (URL-адрес конечной точки маркера).

![Добавление сервера авторизации][api-management-add-authorization-server-2a]

После вставки конечной точки маркера добавьте дополнительный параметр текста с именем **resource**, для которого в качестве значения используйте **URI идентификатора приложения** из приложения AAD для внутренней службы, которая была создана во время публикации проекта Visual Studio.

![URI идентификатора приложения][api-management-aad-sso-uri]

Затем укажите учетные данные клиента. Это учетные данные для ресурса, к которому требуется доступ. В данном случае этим ресурсом является портал разработчика.

![Учетные данные клиента][api-management-client-credentials]

Чтобы получить **идентификатор клиента**, перейдите на вкладку **Настройка** приложения AAD для портала разработчика и скопируйте **идентификатор клиента**.

Чтобы получить **секрет клиента**, щелкните раскрывающийся список **Выбрать длительность** в разделе **Ключи** и укажите интервал. В данном примере используется интервал 1 год.

![идентификатор клиента][api-management-aad-client-id]

Нажмите кнопку **Сохранить** , чтобы сохранить конфигурацию и отобразить ключ. 

> [!IMPORTANT]
> Запишите этот ключ. После закрытия окна конфигурации Azure Active Directory нельзя будет снова отобразить ключ.
> 
> 

Скопируйте ключ в буфер обмена, вернитесь на портал издателя, вставьте ключ в текстовое поле **Секрет клиента** и нажмите кнопку **Сохранить**.

![Добавление сервера авторизации][api-management-add-authorization-server-3]

Сразу после указания учетных данных клиента необходимо предоставить код авторизации. Скопируйте этот код авторизации и вернитесь в приложение портала разработчика Azure AD. Вставьте код авторизации в поле **URL-адрес ответа** и нажмите кнопку **Сохранить** еще раз.

![URL-адрес ответа][api-management-aad-reply-url]

Следующим шагом является настройка разрешений для приложения портала разработчика AAD. Щелкните **Разрешения приложения** и установите флажок для параметра **Чтение данных каталога**. Нажмите кнопку **Сохранить**, чтобы сохранить это изменение, а затем нажмите кнопку **Добавить приложение**.

![Добавление разрешений][api-management-add-devportal-permissions]

Щелкните значок поиска, введите **APIM** в поле "Начинается с", выберите **APIMAADDemo** и щелкните значок флажка, чтобы сохранить изменения.

![Добавление разрешений][api-management-aad-add-app-permissions]

Щелкните **Делегированные разрешения** для **APIMAADDemo** и установите флажок рядом с параметром **Доступ APIMAADDemo**. Нажмите кнопку **Сохранить**. Теперь приложение портала разработчика имеет доступ к внутренней службе.

![Добавление разрешений][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Включение авторизации пользователей OAuth 2.0 для API «Калькулятор».
Теперь, когда сервер OAuth 2.0 настроен, можно указать его параметры безопасности для API. Этот шаг показан в видео начиная с отметки времени 14:30.

Выберите пункт **Интерфейсы API** в меню слева и щелкните **Калькулятор**, чтобы просмотреть и настроить соответствующие параметры.

![API «Калькулятор»][api-management-calc-api]

Откройте вкладку **Безопасность**, установите флажок **OAuth 2.0**, выберите нужный сервер авторизации в раскрывающемся списке **Сервер авторизации** и нажмите кнопку **Сохранить**.

![API «Калькулятор»][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Успешный вызов API «Калькулятор» с портала разработчика
Теперь, когда для API-интерфейса настроена авторизация OAuth 2.0, из центра разработчика можно успешно вызывать его операции. Этот шаг показан в видео начиная с отметки времени 15:00.

На портале разработчика вернитесь к операции **Добавление двух целых** службы "Калькулятор" и нажмите кнопку **Попробовать**. Обратите внимание на новый элемент в разделе **Авторизация** , который соответствует только что добавленному серверу авторизации.

![API «Калькулятор»][api-management-calc-authorization-server]

Выберите **Код авторизации** из раскрывающегося списка и введите данные учетной записи, которую будете использовать. Если вы уже вошли с помощью учетной записи, запрос на ввод учетных данных не появится.

![API «Калькулятор»][api-management-devportal-authorization-code]

Нажмите кнопку **Отправить**. **Состояние ответа** должно быть **200 ОК**. Кроме того, примите к сведению результаты операции в содержимом ответа.

![API «Калькулятор»][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Настройка классического приложения для вызова API
Следующая процедура на видео начинается с отметки времени 16:30. Она настраивает простое классическое приложение для вызова API. Первым шагом является регистрация классического приложения в Azure AD и предоставление ему доступа к каталогу и внутренней службе. Начиная с отметки времени 18:25 видео, можно увидеть, как классическое приложение вызывает операцию API «Калькулятор».

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Настройка политики проверки JWT для запросов предварительной авторизации
Последняя процедура в видеоролике начинается с отметки 20:48. В ней показано, как использовать политику [Проверка JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) для предварительной авторизации запросов путем проверки маркеров доступа каждого входящего запроса. Если запрос не прошел проверку JWT, он блокируется управлением API и не передается во внутреннюю службу.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Другой пример настройки и использования этой политики см. в видео [Облачное покрытие, эпизод 177: другие функции управления API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) с отметки времени 13:50. Перемотайте вперед до отметки времени 15:00, чтобы просмотреть политики, настроенные в редакторе политик, и до 18:50, чтобы просмотреть демонстрацию вызова операции с портала разработчика с обязательным маркером авторизации и без него.

## <a name="next-steps"></a>Дальнейшие действия
* См. другие [видео](https://azure.microsoft.com/documentation/videos/index/?services=api-management) об управлении API.
* Другие способы защиты внутренней службы см. в разделе [Взаимная аутентификация на основе сертификатов](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md
