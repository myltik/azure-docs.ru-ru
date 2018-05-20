---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7e9518f8a90faa0566b96d58992b01e4b0a642f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
## <a name="test-your-code"></a>Тестирование кода

Чтобы протестировать приложение в Visual Studio, нажмите клавишу **F5** для запуска проекта. В браузере откроется адрес http://<span></span>localhost:{port} и отобразится кнопка **Вход с помощью учетной записи Майкрософт**. Нажмите кнопку, чтобы начать процесс входа в систему.

Когда вы будете готовы запустить тестирование, войдите в учетную запись Microsoft Azure Active Directory (рабочую или учебную) (Azure AD) или в личную учетную запись Майкрософт (<span>live.com</span>, <span>outlook.</span>com).

![Войдите с помощью учетной записи Майкрософт](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Войдите в учетную запись Майкрософт](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Просмотр результатов приложения
После входа пользователь перенаправляется на домашнюю страницу веб-сайта. Домашняя страница — это URL-адрес HTTPS, указанный в информации о регистрации приложения на портале регистрации приложений Майкрософт. Домашняя страница содержит приветственное сообщение *"Привет \<пользователь>",* ссылку для выхода из системы и ссылку для просмотра утверждений пользователя. При переходе по ссылке на утверждения пользователя отображается контроллер *утверждения*, созданный ранее.

### <a name="browse-to-see-the-users-claims"></a>Переход для просмотра утверждений пользователя
Для просмотра утверждений пользователей выберите эту ссылку для перехода к представлению контроллера, которое доступно только пользователям, прошедшим проверку подлинности.

#### <a name="view-the-claims-results"></a>Просмотр результатов утверждений
После перехода к представлению контроллера отобразится таблица, содержащая основные свойства пользователя:

|Свойство |Значение |ОПИСАНИЕ |
|---|---|---|
|**Имя** |Полное имя пользователя | Имя и фамилия пользователя.
|**Имя пользователя** |user<span>@domain.com</span> | Имя пользователя, которое используется для идентификации пользователя.
|**Тема** |Субъект |Строка, уникально идентифицирующая пользователя в Интернете.|
|**Идентификатор клиента** |Guid | **Уникальный идентификатор**, который представляет организацию Azure AD пользователя.|

Кроме того, вы увидите таблицу всех утверждений, которые находятся в запросе проверки подлинности. Дополнительные сведения см. в статье [Azure AD token reference](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) (Справочник по токенам Azure AD).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Проверка доступа к методу, включающему атрибут Authorize (необязательно)
Для проверки доступа к контроллеру, защищенному с помощью атрибута `Authorize`, в качестве анонимного пользователя сделайте следующее:
1. Выберите ссылку для выхода пользователя и завершения процесса выхода.
2. В окне браузера введите http://<span></span>localhost:{порт}/claims, чтобы получить доступ к контроллеру, защищенному атрибутом `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Ожидаемые результаты после получения доступа к защищенному контроллеру
Вам будет предложено пройти проверку подлинности, чтобы использовать представление защищенного контроллера.

## <a name="advanced-options"></a>Расширенные параметры

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Защита всего веб-сайта
Для защиты всего веб-сайта в файле **Global.asax** добавьте атрибут `AuthorizeAttribute` к фильтру `GlobalFilters` в методе `Application_Start`.

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Ограничение доступа на вход в приложение
По умолчанию при сборке приложения, созданного в рамках этого руководства, ваше приложение сможет принимать операции входа с помощью личных учетных записей (включая outlook.com, live.com и другие), а также рабочих и учебных учетных записей из любой компании или организации, которая использует Azure Active Directory. Этот вариант рекомендуется для приложений SaaS.

Ограничить доступ для входа в систему для приложения можно несколькими способами.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Способ 1. Разрешение входа в приложение только пользователям отдельного экземпляра Active Directory организации (приложение с одним клиентом)

Это распространенный сценарий для *бизнес-приложений*. Если требуется, чтобы приложение принимало операции входа только с использованием учетных записей, относящихся к определенному экземпляру Azure Active Directory (включая *гостевые учетные записи* этого экземпляра), сделайте следующее:

1. В файле **web.config** для значения параметра `Tenant` вместо `Common` установите имя клиента организации (например, `contoso.onmicrosoft.com`).
2. В классе [OWIN Startup](#configure-the-authentication-pipeline) задайте аргументу `ValidateIssuer` значение `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Способ 2. Ограничение доступа к приложению для пользователей в определенном списке организаций

Доступ на вход в приложение можно ограничить учетными записями пользователей организации Azure AD, которая находится в списке разрешенных организаций:
1. В классе [OWIN Startup](#configure-the-authentication-pipeline) задайте аргументу `ValidateIssuer` значение `true`.
2. Установите список разрешенных организаций в качестве значения параметра `ValidIssuers`.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Способ 3. Использование пользовательского метода для проверки издателей
Пользовательский метод можно реализовать для проверки издателей с помощью параметра **IssuerValidator**. Дополнительные сведения о том, как использовать этот параметр, см. в статье [TokenValidationParameters Class](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) (Класс TokenValidationParameters) на MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
