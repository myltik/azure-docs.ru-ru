---
title: Защита внутренней службы веб-API с помощью Azure Active Directory и управления API
description: Информация о защите внутренней службы веб-API с помощью Azure Active Directory и управления API
services: api-management
documentationcenter: ''
author: steved0x
manager: erikre
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2016
ms.author: sdanie

---
# Защита внутренней службы веб-API с помощью Azure Active Directory и управления API
На следующих видео показано, как собрать внутреннюю службу веб-API и защитить ее, используя протокол OAuth 2.0 с Azure Active Directory и управлением API. Эта статья содержит обзор инструкций на видео и дополнительные сведения к ним. Посмотрев этот 24-минутный ролик, вы познакомитесь со следующими темами.

* Создание серверной части веб-API и обеспечение ее безопасности при помощи AAD ― с 1:30
* Импорт API в управление API ― с 7:10
* Настройка портала разработчика на вызов API ― с 9:09
* Настройка классического приложения для вызова API ― с 18:08
* Настройка политики проверки JWT для предварительной авторизации запросов ― с 20:47

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## Создание каталога Azure AD
Для защиты внутренней службы веб-API с помощью Azure Active Directory сначала необходимо установить клиент AAD. В этом видеоролике используется клиент с именем **APIMDemo**. Чтобы создать клиент AAD, войдите на [классический портал Azure](https://manage.windowsazure.com) и нажмите кнопку **Создать**->**Службы приложений**->**Active Directory**->**Каталог**->**Настраиваемое создание**.

![Azure Active Directory][api-management-create-aad-menu]

В этом примере каталог с именем **APIMDemo** создается в домене по умолчанию с именем **DemoAPIM.onmicrosoft.com**. Этот каталог используется в видео.

![Azure Active Directory][api-management-create-aad]

## Создание службы веб-API, защищенной с помощью Azure Active Directory
На этом шаге создается внутренняя служба веб-API с помощью Visual Studio 2013. Этот шаг начинается на видео с отметки времени 1:30. Для создания проекта внутренней службы веб-API в Visual Studio выберите **Файл**->**Создать**->**Проект** и выберите **Веб-приложение ASP.NET** из списка **Веб**-шаблонов. В этом видео проекту присвоено имя **APIMAADDemo**. Нажмите кнопку **ОК**, чтобы создать проект.

![Visual Studio][api-management-new-web-app]

Чтобы создать проект веб-API, выберите **Веб-API** из списка **Выбор шаблона**. Чтобы настроить проверку подлинности Azure Active Directory, щелкните **Изменить проверку подлинности**.

![Новый проект][api-management-new-project]

Выберите **Учетные записи организации** и укажите **Домен** клиента AAD. В этом примере используется домен **DemoAPIM.onmicrosoft.com**. Домен каталога можно найти на вкладке **Домены** вашего каталога.

![Домены][api-management-aad-domains]

Настройте нужные параметры в диалоговом окне **Изменение аутентификации** и нажмите кнопку **ОК**.

![Изменение проверки подлинности][api-management-change-authentication]

После того как вы нажмете кнопку **ОК**, Visual Studio попытается зарегистрировать приложение с каталогом Azure AD, и вам будет предложено войти в Visual Studio. Войдите с помощью учетной записи администратора вашего каталога.

![Вход в Visual Studio][api-management-sign-in-vidual-studio]

Чтобы настроить этот проект как веб-API Azure, установите флажок рядом с параметром **Разместить в облаке** и нажмите кнопку **ОК**.

![Новый проект][api-management-new-project-cloud]

Может появиться запрос на вход в Azure, после входа вы сможете настроить веб-приложение.

![Настройка][api-management-configure-web-app]

В этом примере создается новый **План службы приложений** с именем **APIMAADDemo**.

Нажмите кнопку **ОК**, чтобы настроить веб-приложение и создать проект.

## Добавление кода в проект веб-API
На следующем шаге в видео добавляется код в проект веб-API. Этот шаг начинается с отметки времени 4:35.

Веб-API в этом примере реализует простую службу «Калькулятор» с помощью модели и контроллера. Чтобы добавить модель в службу, щелкните правой кнопкой мыши **Модели** в **Обозревателе решений** и выберите последовательно **Добавить**, **Класс**. Присвойте классу имя `CalcInput` и нажмите **Добавить**.

Добавьте следующий оператор `using` в верхнюю часть файла `CalcInput.cs`:

    using Newtonsoft.Json;

 Замените созданный класс следующим кодом:

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Щелкните правой кнопкой мыши **Контроллеры** в **Обозревателе решений** и выберите последовательно **Добавить**->**Контроллер**. Выберите элемент **Web API 2 Controller - Empty** (Контроллер веб-интерфейса API 2 — пустой) и нажмите кнопку **Добавить**. Введите имя контроллера **CalcController** и нажмите кнопку**Добавить**.

![Добавление контролера][api-management-add-controller]

Добавьте следующий оператор `using` в верхнюю часть файла `CalcController.cs`:

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Замените созданный контроллер следующим кодом: Этот код реализует операции `Add`, `Subtract`, `Multiply` и `Divide` простого API «Калькулятор».

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

Нажмите клавишу **F6**, чтобы построить и проверить решение.

## Публикация проекта в Azure
На этом этапе проект Visual Studio публикуется в Azure. Этот шаг начинается на видео с отметки времени 5:45.

Чтобы опубликовать проект в Azure, щелкните правой кнопкой мыши проект **APIMAADDemo** в Visual Studio и выберите **Опубликовать**. Сохраните параметры по умолчанию в диалоговом окне **Веб-публикация** и нажмите кнопку **Опубликовать**.

![Веб-публикация][api-management-web-publish]

## Предоставление разрешений для приложения внутренней службы Azure AD
Новое приложение для внутренней службы создается в каталоге Azure AD как часть процесса настройки и публикации проекта веб-API. На этом шаге видео, который начинается с отметки времени 6:13, разрешения предоставляются для внутренней службы веб-API.

![Приложение][api-management-aad-backend-app]

Щелкните имя приложения, чтобы настроить необходимые разрешения. На вкладке **Настройка** прокрутите страницу вниз до раздела **Разрешения для других приложений**. Щелкните раскрывающийся список **Разрешения приложений** рядом с **Microsoft** **Azure Active Directory**, установите флажок рядом с разрешением **Чтение данных каталога** и нажмите кнопку **Сохранить**.

![Добавление разрешений][api-management-aad-add-permissions]

> [!NOTE]
> Если приложение **Microsoft** **Azure Active Directory** не указано в списке разрешений для других приложений, нажмите кнопку **Добавить приложение** и добавьте его из списка.
> 
> 

Запишите **URI идентификатора приложения**, чтобы использовать его на следующем шаге во время настройки приложения Azure AD для портала разработчика управления API.

![URI идентификатора приложения][api-management-aad-sso-uri]

## Импорт веб-API в управление API
API-интерфейсы настраиваются на портале издателя API, на который можно перейти с классического портала Azure. Чтобы перейти на портал издателя, найдите на классическом портале Azure службу управления API и нажмите кнопку **Управление**. Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Создание экземпляра управления API] в руководстве [Начало работы со службой управления Azure API][Начало работы со службой управления Azure API].

![Портал издателя][api-management-management-console]

Операции можно [добавить в API-интерфейсы вручную](api-management-howto-add-operations.md) или импортировать их. В этом видео операции импортируются в формате Swagger начиная с отметки времени 6:40.

Создайте файл `calcapi.json` с приведенным ниже содержимым и сохраните его на компьютер. Убедитесь, что атрибут `host` указывает на серверную часть веб-API. В этом примере используется `"host": "apimaaddemo.azurewebsites.net"`.

{ "swagger": "2.0", "info": { "title": "Calculator", "description": "Arithmetics over HTTP!", "version": "1.0" }, "host": "apimaaddemo.azurewebsites.net", "basePath": "/api", "schemes": [ "http" ], "paths": { "/add?a={a}&b={b}": { "get": { "description": "Responds with a sum of two numbers.", "operationId": "Add two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand. Default value is <code>51</code>.", "required": true, "default": "51", "enum": [ "51" ] }, { "name": "b", "in": "query", "description": "Second operand. Default value is <code>49</code>.", "required": true, "default": "49", "enum": [ "49" ] } ], "responses": {} } }, "/sub?a={a}&b={b}": { "get": { "description": "Responds with a difference between two numbers.", "operationId": "Subtract two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand. Default value is <code>100</code>.", "required": true, "default": "100", "enum": [ "100" ] }, { "name": "b", "in": "query", "description": "Second operand. Default value is <code>50</code>.", "required": true, "default": "50", "enum": [ "50" ] } ], "responses": {} } }, "/div?a={a}&b={b}": { "get": { "description": "Responds with a quotient of two numbers.", "operationId": "Divide two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand. Default value is <code>100</code>.", "required": true, "default": "100", "enum": [ "100" ] }, { "name": "b", "in": "query", "description": "Second operand. Default value is <code>20</code>.", "required": true, "default": "20", "enum": [ "20" ] } ], "responses": {} } }, "/mul?a={a}&b={b}": { "get": { "description": "Responds with a product of two numbers.", "operationId": "Multiply two integers", "parameters": [ { "name": "a", "in": "query", "description": "First operand. Default value is <code>20</code>.", "required": true, "default": "20", "enum": [ "20" ] }, { "name": "b", "in": "query", "description": "Second operand. Default value is <code>5</code>.", "required": true, "default": "5", "enum": [ "5" ] } ], "responses": {} } } } }

Чтобы импортировать API калькулятора, щелкните **API** в расположенном слева меню **Управление API**, а затем выберите **Импортировать API**.

![Кнопка импорта API][api-management-import-api]

Выполните следующие действия, чтобы настроить API калькулятора.

1. Щелкните **Из файла**, перейдите к сохраненному файлу `calculator.json` и нажмите переключатель **Swagger**.
2. В текстовом поле **Суффикс URL-адреса веб-API** введите **calc**.
3. Щелкните в поле **Продукты (необязательно)** и выберите **Starter**.
4. Щелкните **Сохранить**, чтобы импортировать API.

![Добавление нового API][api-management-import-new-api]

После импорта API на портале издателя выводится страница сводных данных для API.

## Неудавшийся вызов API с портала разработчика
На этом этапе API импортирован в управление API, но его еще нельзя успешно вызвать с портала разработчика, поскольку внутренняя служба защищена с помощью проверки подлинности Azure AD. Это продемонстрировано на видео на отметке времени 7:40 следующим образом.

Выберите **Портал разработчика** справа вверху на портале издателя.

![Портал разработчика][api-management-developer-portal-menu]

Щелкните **Интерфейсы API** и выберите API **Калькулятор**.

![Портал разработчика][api-management-dev-portal-apis]

Щелкните **Попробовать**.

![Попробовать][api-management-dev-portal-try-it]

Нажмите кнопку **Отправить** и обратите внимание на состояние ответа **401 — Не санкционировано**.

![Отправка][api-management-dev-portal-send-401]

Запрос не авторизован, поскольку внутренняя служба API защищена службой Azure Active Directory. Для успешного вызова API портал разработчика необходимо настроить, чтобы авторизовать разработчиков, использующих OAuth 2.0. Этот процесс описывается в следующих разделах.

## Регистрация портала разработчика как приложения AAD
Чтобы настроить портал разработчика для авторизации разработчиков, использующих OAuth 2.0, сначала необходимо зарегистрировать портал разработчика как приложение AAD. На видео этот процесс показан с отметки времени 8:27.

Перейдите к клиенту Azure AD, который использовался в первом шаге этого видео (в нашем примере это клиент **APIMDemo**), и откройте вкладку **Приложения**.

![Новое приложение][api-management-aad-new-application-devportal]

Нажмите кнопку **Добавить**, чтобы создать новое приложение Azure Active Directory, а затем установите флажок **Добавить приложение, разрабатываемое моей организацией**.

![Новое приложение][api-management-new-aad-application-menu]

Выберите **Веб-приложение и/или веб-API**, введите имя и нажмите стрелку «Далее». В этом примере используется **APIMDeveloperPortal**.

![Новое приложение][api-management-aad-new-application-devportal-1]

В поле **URL-адрес входа** введите URL-адрес службы управления API и добавьте `/signin`. В этом примере используется **https://contoso5.portal.azure-api.net/signin **.

В поле **URL-адрес идентификатора приложения** введите URL-адрес службы управления API и добавьте несколько уникальных символов. Это могут быть любые символы. В данном примере используется **https://contoso5.portal.azure-api.net/dp**. Настроив нужные **Свойства приложения**, установите флажок для создания приложения.

.![Новое приложение][api-management-aad-new-application-devportal-2]

## Настройка сервера авторизации OAuth 2.0 в управлении API
На следующем шаге будет настроен сервер авторизации OAuth 2.0 в управлении API Этот шаг показан в видео начиная с отметки времени 9:43.

В меню «Управление API» слева выберите пункт **Безопасность**, перейдите на вкладку **OAuth 2.0** и щелкните **Добавить сервер авторизации**.

![Добавление сервера авторизации][api-management-add-authorization-server]

В полях **Name** (Имя) и **Description** (Описание) введите имя и (при желании) описание. Эти поля служат для идентификации сервера авторизации OAuth 2.0 в текущем экземпляре службы управления API. В этом примере используется **демоверсия сервера авторизации**. Позже, когда вы укажете сервер OAuth 2.0 проверки подлинности для API, вам нужно будет выбрать это имя.

В поле **URL-адрес страницы регистрации клиента** введите значение заполнителя, например `http://localhost`. **URL-адрес страницы регистрации клиента** указывает на страницу, на которой пользователи могут создавать и настраивать собственные учетные записи для поставщиков OAuth 2.0, поддерживающих пользовательское управление учетными записями. В этом примере пользователи не создают и не настраивают собственные учетные записи, поэтому используется заполнитель.

![Добавление сервера авторизации][api-management-add-authorization-server-1]

Затем укажите **URL-адрес конечной точки авторизации** и **URL-адрес конечной точки маркера**.

![Сервер авторизации][api-management-add-authorization-server-1a]

Эти значения можно получить на странице **Конечные точки приложения** для приложения AAD, которое вы создали для портала разработчика. Для доступа к конечным точкам откройте вкладку **Настройка** приложения AAD и нажмите кнопку **Просмотреть конечные точки**.

![Приложение][api-management-aad-devportal-application]

![Просмотр конечных точек][api-management-aad-view-endpoints]

Скопируйте значение **Конечная точка авторизации OAuth 2.0** и вставьте его в текстовое поле **URL-адрес конечной точки авторизации**.

![Добавление сервера авторизации][api-management-add-authorization-server-2]

Скопируйте значение **Конечная точка маркера OAuth 2.0** и вставьте его в текстовое поле **URL-адрес конечной точки маркера**.

![Добавление сервера авторизации][api-management-add-authorization-server-2a]

После вставки конечной точки маркера добавьте дополнительный параметр текста с именем **resource**, для которого в качестве значения используйте **URI идентификатора приложения** из приложения AAD для внутренней службы, которая была создана во время публикации проекта Visual Studio.

![URI идентификатора приложения][api-management-aad-sso-uri]

Затем укажите учетные данные клиента. Это учетные данные для ресурса, к которому требуется доступ. В данном случае этим ресурсом является внутренняя служба.

![Учетные данные клиента][api-management-client-credentials]

Чтобы получить **идентификатор клиента**, перейдите на вкладку **Настройка** приложения AAD для внутренней службы и скопируйте **идентификатор клиента**.

Чтобы получить **секрет клиента**, щелкните раскрывающийся список **Выбор длительности** в разделе **Ключи** и укажите интервал. В данном примере используется интервал 1 год.

![Идентификатор клиента][api-management-aad-client-id]

Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию и отобразить ключ.

> [!IMPORTANT]
> Запишите этот ключ. После закрытия окна конфигурации Azure Active Directory нельзя будет снова отобразить ключ.
> 
> 

Скопируйте ключ в буфер обмена, вернитесь на портал издателя, вставьте ключ в текстовое поле **Секрет клиента** и нажмите кнопку **Сохранить**.

![Добавление сервера авторизации][api-management-add-authorization-server-3]

Сразу после указания учетных данных клиента необходимо предоставить код авторизации. Скопируйте этот код авторизации и вернитесь в приложение портала разработчика Azure AD. Вставьте код авторизации в поле **URL-адрес ответа** и нажмите кнопку **Сохранить** еще раз.

![URL-адрес ответа][api-management-aad-reply-url]

Следующим шагом является настройка разрешений для приложения портала разработчика AAD. Щелкните **Разрешения приложения** и установите флажок для параметра **Чтение данных каталога**. Нажмите кнопку **Сохранить**, чтобы сохранить это изменение, затем нажмите кнопку **Добавить приложение**.

![Добавление разрешений][api-management-add-devportal-permissions]

Щелкните значок поиска, введите **APIM** в поле «Начинается с», выберите **APIMAADDemo** и щелкните флажок, чтобы сохранить изменения.

![Добавление разрешений][api-management-aad-add-app-permissions]

Щелкните **Делегировать разрешения** для **APIMAADDemo** и установите флажок рядом с параметром **Доступ APIMAADDemo**. Нажмите кнопку **Сохранить**. Теперь приложение портала разработчика имеет доступ к внутренней службе.

![Добавление разрешений][api-management-aad-add-delegated-permissions]

## Включение авторизации пользователей OAuth 2.0 для API «Калькулятор».
Теперь, когда сервер OAuth 2.0 настроен, можно указать его параметры безопасности для API. Этот шаг показан в видео начиная с отметки времени 14:30.

Выберите пункт **Интерфейсы API** в меню слева и щелкните **Калькулятор**, чтобы просмотреть и настроить соответствующие параметры.

![API «Калькулятор»][api-management-calc-api]

Откройте вкладку **Безопасность**, установите флажок **OAuth 2.0**, выберите нужный сервер авторизации из раскрывающегося списка **Сервер авторизации** и нажмите кнопку **Сохранить**.

![API «Калькулятор»][api-management-enable-aad-calculator]

## Успешный вызов API «Калькулятор» с портала разработчика
Теперь, когда для API-интерфейса настроена авторизация OAuth 2.0, из центра разработчика можно успешно вызывать его операции. Этот шаг показан в видео начиная с отметки времени 15:00.

На портале разработчика вернитесь к операции **Добавление двух целых** службы «Калькулятор» и нажмите кнопку **Попробовать**. Обратите внимание на новый элемент в разделе **Авторизация**, который соответствует только что добавленному серверу авторизации.

![API «Калькулятор»][api-management-calc-authorization-server]

Выберите **Код авторизации** из раскрывающегося списка и введите данные учетной записи, которую будете использовать. Если вы уже вошли с помощью учетной записи, запрос на ввод учетных данных не появится.

![API «Калькулятор»][api-management-devportal-authorization-code]

Нажмите **Отправить** и обратите внимание на **Состояние ответа** (**200 ОК**) и результаты операции в содержимом ответа.

![API «Калькулятор»][api-management-devportal-response]

## Настройка классического приложения для вызова API
Следующая процедура на видео начинается с отметки времени 16:30. Она настраивает простое классическое приложение для вызова API. Первым шагом является регистрация классического приложения в Azure AD и предоставление ему доступа к каталогу и внутренней службе. Начиная с отметки времени 18:25 видео, можно увидеть, как классическое приложение вызывает операцию API «Калькулятор».

## Настройка политики проверки JWT для запросов предварительной авторизации
Последняя процедура в видеоролике начинается с 20:48. В ней показано, как использовать политику [Проверка JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) для предварительной авторизации запросов путем проверки маркеров доступа каждого входящего запроса. Если запрос не прошел проверку JWT, он блокируется управлением API и не передается во внутреннюю службу.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Другой пример настройки и использования этой политики см. на видео [Облачное покрытие, эпизод 177: другие функции управления API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) с отметки времени 13:50. Перемотайте вперед до отметки времени 15:00, чтобы просмотреть политики, настроенные в редакторе политик, и до 18:50, чтобы просмотреть демонстрацию вызова операции с портала разработчика с обязательным маркером авторизации и без него.

## Дальнейшие действия
* См. другие [видео](https://azure.microsoft.com/documentation/videos/index/?services=api-management) об управлении API.
* Другие способы защиты внутренней службы см. в статьях [Взаимная проверка подлинности сертификатов](api-management-howto-mutual-certificates.md) и [Подключение через VPN или ExpressRoute](api-management-howto-setup-vpn.md).

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

[Создание экземпляра управления API]: api-management-get-started.md#create-service-instance
[Начало работы со службой управления Azure API]: api-management-get-started.md

<!---HONumber=AcomDC_0810_2016-->