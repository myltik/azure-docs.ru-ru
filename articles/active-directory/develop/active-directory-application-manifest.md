---
title: "Основные сведения о манифесте приложения Azure Active Directory | Документация Майкрософт"
description: "Подробное описание манифеста приложения Azure Active Directory, который представляет конфигурацию удостоверений приложения в клиенте Azure AD и используется для упрощения авторизации OAuth, предоставления согласия и многого другого."
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: f3284d4cbb15f21522549c678410815b54344744
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-application-manifest"></a>Манифест приложения Azure Active Directory
Приложения, которые интегрируются с Azure AD необходимо зарегистрировать в клиент Azure AD. Это приложение можно настроить с помощью манифеста приложения (в колонке Azure AD) в [портал Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Ссылку на манифест

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Ключ  |Тип значения |Пример значения  |ОПИСАНИЕ  |
|---------|---------|---------|---------|
|ИД приложения     |  Строка идентификатора       |""|  Уникальный идентификатор для приложения, которое назначается для приложения Azure AD.|
|appRoles     |    Тип массива     |[{<br>&emsp;«allowedMemberTypes»: [<br>&emsp;&nbsp;&nbsp;&nbsp;«Пользователь»<br>&emsp;],<br>&emsp;«Описание»: «Чтение только доступ к сведений об устройстве»,<br>&emsp;«displayName»: «Только чтение»,<br>&emsp;«Идентификатор»: идентификатор guid,<br>&emsp;«isEnabled»: true<br>&emsp;«значение»: «ReadOnly»<br>}]|Коллекция ролей, которые приложение может объявлять. Эти роли могут назначаться пользователям, группам или субъектам-службам.|
|AvailableToOtherTenants|Логическое|Да|Если это значение равно true, приложение станет доступным для других клиентов.  Если значение равно false, приложение только объем клиенту она зарегистрирована в.  Дополнительные сведения см.: [вход в любой пользователь Azure Active Directory (AD), используя шаблон многопользовательского приложения](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |строка         |MyRegisteredApp         |Отображаемое имя для приложения. |
|errorURL     |строка         |http:<i></i>//MyRegisteredAppError         |URL-адрес для ошибок, возникающих в приложении. |
|GroupMembershipClaims     |    строка     |    1     |   Битовая маска, настраивающая утверждение «groups», выданное в пользователя или токене доступа OAuth 2.0, которое ожидает приложение. Значения битовой маски: 0: нет, 1: группы безопасности и роли Azure AD, 2: зарезервировано и 4: зарезервированные. Значение битовой маски 7 позволяет получить все группы безопасности, группы рассылки и роли каталога Azure AD, в которые входит выполнивший вход пользователь.      |
|optionalClaims     |  строка       |     null    |    Необязательный утверждений, возвращенную в токене службе маркеров безопасности для этого конкретного приложения.     |
|acceptMappedClaims    |      Логическое   | Да        |    Если это значение равно true, это позволяет приложению использовать утверждения сопоставления без указания пользовательского ключа подписи.|
|Домашняя страница     |  строка       |http:<i></i>//MyRegistererdApp         |    URL-адрес домашней страницы приложения.     |
|identifierUris     |  Массив строк       | http:<i></i>//MyRegistererdApp        |   Определяемые пользователем URI(s), уникальным образом идентифицирующие веб-приложения, его клиент Azure AD или в проверенный пользовательский домен, если приложение является мультитенантным.      |
|keyCredentials     |   Тип массива      |   [{<br>&nbsp;«customKeyIdentifier»: значение null<br>«Дата окончания»: "2018-09-13T00:00:00Z»,<br>«Идентификатор» ключа:»\<guid > «,<br>«startDate»: "2017 г-09-12T00:00:00Z»,<br>«Тип»: «AsymmetricX509Cert»<br>«Использование»: «Проверить»<br>«value»: null<br>}]      |   Это свойство содержит ссылки на приложения назначенных учетных данных, общие секреты на основе строк и сертификаты X.509.  Эти учетные данные следует учитывать при запросе маркера доступа (когда приложение работает в качестве клиента вместо его в качестве ресурса).     |
|knownClientApplications     |     Тип массива    |    [guid]     |     Значение используется для объединение согласие, если у вас есть решение, содержащее состоит из двух частей, клиентское приложение и пользовательский веб-API. Если appID клиентского приложения, введите в это значение, пользователь может быть только один раз согласие на клиентское приложение. Azure AD узнает, что подтверждения согласия клиента означает неявно соглашаетесь веб-API и автоматически выбирает субъекты-службы для клиента и веб-API в то же время (клиент и веб-API необходимо зарегистрировать в одном клиент).|
|logoutUrl     |   строка      |     http:<i></i>//MyRegisteredAppLogout    |   URL-адрес выхода из приложения.      |
|oauth2AllowImplicitFlow     |   Логическое      |  false       |       Указывает, может ли это веб-приложение запрашивать неявные токены потока OAuth2.0. Значение по умолчанию — false. Используется для приложений на основе браузера, похожи на одной странице приложения Javascript. |
|oauth2AllowUrlPathMatching     |   Логическое      |  false       |   Указывает, будет ли в рамках запросов токенов OAuth 2.0 Azure AD разрешать сопоставление пути URI перенаправления со значением replyUrls приложения. Значение по умолчанию — false.      |
|oauth2Permissions     | Тип массива         |      [{<br>«adminConsentDescription»: «Разрешить приложению доступ к ресурсам от имени пользователя, выполнившего вход.»,<br>«adminConsentDisplayName»: «Resource1 доступа»,<br>«Идентификатор»:»\<guid > «,<br>«isEnabled»: true<br>«Тип»: «User»,<br>«userConsentDescription»: «Разрешить приложению доступ к resource1 от вашего имени.»,<br>«userConsentDisplayName»: «Доступ к ресурсам»<br>«value»: «user_impersonation»<br>}]   |  Коллекция областей разрешений OAuth 2.0, которые веб-API (ресурс) предоставляет клиентским приложениям. Эти области разрешений могут предоставляться клиентским приложениям во время согласия. |
|oauth2RequiredPostResponse     | Логическое        |    false     |      Указывает, будет ли в рамках запросов токенов OAuth 2.0 Azure AD будет разрешать запросы POST помимо запросов GET. Значение по умолчанию равно false, которое указывает, что будет разрешено только запросов GET.   
|objectId     | Строка идентификатора        |     ""    |    Уникальный идентификатор для приложения в каталоге.  Это не идентификатор, используемый для идентификации приложения в любой протокол транзакций.  Это пользователь для ссылки на объект в запросы к каталогу.|
|passwordCredentials     | Тип массива        |   [{<br>«customKeyIdentifier»: значение null<br>«Дата окончания»: "2018-10-19T17:59:59.6521653Z»,<br>«Идентификатор» ключа:»\<guid > «,<br>«startDate»: "2016-10-19T17:59:59.6521653Z»,<br>«value»: null<br>}]      |    См. в описании свойства keyCredentials.     |
|PublicClient     |  Логическое       |      false   | Указывает, является ли приложение общедоступным клиентом (такие как установленное приложение, работающее на мобильном устройстве). Значение по умолчанию — false.        |
|supportsConvergence     |  Логическое       |   false      | Не следует изменять это свойство.  Примите значение по умолчанию.        |
|значением replyUrls     |  Массив строк       |   http:<i></i>//localhost     |  Это свойство многозначности хранит список зарегистрированных redirect_uri значений, которые Azure AD будет принимать как как назначения при returining маркеры. |
|RequiredResourceAccess     |     Тип массива    |    [{<br>«resourceAppId»: «00000002-0000-0000-c000-000000000000»<br>«resourceAccess»: [{}<br>&nbsp;&nbsp;&nbsp;&nbsp;«Идентификатор»: «311a71cc-e848-46a1-bdf8-97ff7156d8e6»<br>&nbsp;&nbsp;&nbsp;&nbsp;«Тип»: «Scope»<br>&nbsp;&nbsp;}]<br>}]     |   Указывает ресурсы, которые этого приложения требуется доступ и набор областей разрешений OAuth и ролей приложения, которые ему необходимы в каждом из этих ресурсов. Эта предварительная настройка доступа к необходимым ресурсам выполняется при взаимодействии согласия.|
|resourceAppId     |    Строка идентификатора     |  ""      |   Уникальный идентификатор для ресурса, которому требуется доступ к приложению. Это значение должно быть равно appId, объявленное в приложении целевого ресурса.     |
|resourceAccess     |  Тип массива       | См. Пример значения свойства requiredResourceAccess.        |   Список областей разрешений OAuth2.0 и ролей приложения, которые требуются приложению из указанного ресурса (содержит идентификатор и тип значения указанные ресурсы)        |
|samlMetadataUrl|строка|http:<i></i>//MyRegisteredAppSAMLMetadata|URL-адрес метаданных SAML для приложения.| 

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о связи объектов приложения и субъекта-службы приложения см. в разделе [объекты участника службы в Azure AD, приложения и][AAD-APP-OBJECTS].
* Определения некоторых основных понятий для разработчиков Azure Active Directory (AD) см. в [глоссарии разработчика по Azure AD][AAD-DEVELOPER-GLOSSARY].

Используйте следующий раздел комментарии для предоставления отзывов, которая позволяет уточнить и отформатировать содержимое веб-узла.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

