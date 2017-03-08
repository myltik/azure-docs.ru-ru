---
title: "Руководство разработчика по Azure Active Directory | Документация Майкрософт"
description: "Данная статья содержит полное руководство по ориентированным на разработчиков ресурсам для Azure Active Directory"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 8da480acc04922a3636821c5303f6380236c5ea3
ms.openlocfilehash: 1bff76371ea1ca3ee68c9f04ea24a42f95bb7da7
ms.lasthandoff: 02/17/2017


---
# <a name="azure-active-directory-developers-guide"></a>Руководство разработчика по Azure Active Directory
## <a name="overview"></a>Обзор
Azure Active Directory (AD) — это платформа для управления удостоверениями, которая доступна как служба (IDMaaS). Она позволяет разработчикам легко интегрировать в свои приложения функции управления удостоверениями. В следующих статьях содержатся основные сведения о реализации и основные функции платформы Azure AD. Мы советуем ознакомиться с ними по порядку или, если вы готовы приступить к работе, сразу перейти к разделу [Приступая к работе](#getting-started).

1. [Интеграция с Azure Active Directory.](active-directory-how-to-integrate.md) Узнайте, почему интеграция с Azure AD является лучшим решением для обеспечения безопасного входа и авторизации.
2. [Сценарии аутентификации в Azure Active Directory.](active-directory-authentication-scenarios.md) Обеспечьте вход в приложение, используя упрощенную проверку подлинности в Azure AD.
3. [Интеграция приложений с Azure Active Directory.](active-directory-integrating-applications.md) Узнайте, как добавлять, обновлять и удалять приложения из Azure AD, а также изучите рекомендации по добавлению фирменной символики в интегрированные приложения.
4. [API Graph Azure Active Directory.](active-directory-graph-api.md) Используйте API Graph в Azure AD для программного доступа к Azure AD через конечные точки REST API. Интерфейс API Graph в Azure AD доступен также через [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph — это унифицированный API-интерфейс, обеспечивающий доступ к нескольким API-интерфейсам облачной службы Майкрософт через одну конечную точку REST API и с использованием единого маркера доступа.
5. [Библиотеки проверки подлинности Azure Active Directory.](active-directory-authentication-libraries.md) Легко проверяйте подлинность пользователей, которым требуются маркеры доступа, с помощью библиотек проверки подлинности Azure AD для .NET, JavaScript, Objective-C, Android и т. д.

## <a name="getting-started"></a>Приступая к работе
Эти учебники предназначены для нескольких платформ и помогут вам быстро приступить к разработке приложений с помощью Azure Active Directory. Вам необходим [клиент Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Руководства по быстрому запуску мобильных приложений и приложений для ПК
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | 
|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Универсальная платформа</br>Windows](active-directory-devquickstarts-windowsstore.md) |

|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|
|[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Прямая интеграция</br>с OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Краткие руководства по началу работы с веб-приложениями
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) |
|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Прямая интеграция</br>с OpenID Connect](active-directory-protocols-openid-connect-code.md) |

| [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|
|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Прямая интеграция</br>с OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Краткие руководства по началу работы с веб-интерфейсами API
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>Руководства по быстрому запуску запросов каталога
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [Graph API](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Инструкции
В этих статьях описываются способы выполнения конкретных задач с использованием Azure Active Directory.

* [Получение клиента Azure AD](active-directory-howto-tenant.md)
* [How to sign in any Azure Active Directory (AD) user using the multi-tenant application pattern](active-directory-devhowto-multi-tenant-overview.md)
* [Использование сертификата вместо секретного ключа для проверки подлинности удостоверения приложения](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* Включение единого входа для нескольких приложений с помощью ADAL на устройствах [Android](active-directory-sso-android.md) и [iOS](active-directory-sso-ios.md)
* [How to get AppSource Certified for Azure Active Directory (AD)](active-directory-devhowto-appsource-certified.md)
* [Добавление приложения в коллекцию приложений Azure Active Directory](active-directory-app-gallery-listing.md)
* [Отправка веб-приложений для Office 365 на панель мониторинга продавца](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Register your application with your Azure Active Directory tenant](../active-directory-app-registration.md) (Регистрация приложения в Azure Active Directory с помощью портала Azure)
* [Основные сведения о манифесте приложения Azure Active Directory](active-directory-application-manifest.md)
* [Изучите рекомендации по фирменной символике для кнопок входа и приобретения приложения в клиентском приложении](active-directory-branding-guidelines.md)
* [Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении](active-directory-appmodel-v2-overview.md)
* [Azure Active Directory B2C: регистрация и вход пользователей в приложения](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Preview: Configuring token lifetimes in Azure AD using PowerShell](../active-directory-configurable-token-lifetimes.md) (Настройка срока службы маркера в Azure AD с помощью PowerShell (предварительная версия)). Дополнительные сведения о настройке срока службы маркера в API Azure AD Graph см. в статье [Policy operations](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) (Операции с политиками) и разделе [Policy entity](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) (Сущность политики).

## <a name="reference"></a>Справочные материалы
Эти статьи содержат основные справочники по REST и API библиотек проверки подлинности, протоколам, ошибкам, образцам кода и конечных точкам.  

### <a name="support"></a>Поддержка
* [Вопросы с тегами.](http://stackoverflow.com/questions/tagged/azure-active-directory) Здесь вы найдете решения Azure Active Directory, связанные с переполнением стека, по тегам [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) и [adal](http://stackoverflow.com/questions/tagged/adal).
* Определения самых распространенных терминов, относящихся к разработке и интеграции приложений, см. в статье [Глоссарий по Azure Active Directory для разработчика](active-directory-dev-glossary.md).

### <a name="code"></a>Код
* [Библиотеки Azure Active Directory с открытым исходным кодом.](http://github.com/AzureAD) Чтобы быстро найти исходный код библиотеки, воспользуйтесь нашим [списком библиотек](active-directory-authentication-libraries.md).
* [Примеры кода Azure Active Directory.](https://github.com/azure-samples?query=active-directory) Самый простой способ навигации по списку примеров — это использование [индекса примеров кода](active-directory-code-samples.md).
* [Библиотека проверки подлинности Active Directory (ADAL) для .NET.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) Справочная документация доступна как для [последней основной версии](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory), так и для [предыдущей](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API Graph
* [Справочник по API Graph.](https://msdn.microsoft.com/library/azure/hh974476.aspx) Справочник по REST для API Graph Azure Active Directory. [Просмотрите интерактивный справочник по API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Области разрешений API Graph.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) Области разрешений OAuth 2.0, которые необходимы для управления доступом приложения к данным каталога в клиенте.

### <a name="authentication-and-authorization-protocols"></a>Протоколы аутентификации и авторизации
* [Смена ключей подписывания Azure Active Directory.](active-directory-signing-key-rollover.md) Сведения о смене ключей подписывания в Azure AD и о том, как обновлять ключи для самых распространенных сценариев приложений.
* [Авторизация доступа к веб-приложениям с помощью OAuth 2.0 и Azure Active Directory.](active-directory-protocols-oauth-code.md) Протокол OAuth 2.0 позволяет предоставлять доступ к веб-приложениям и веб-интерфейсам API в клиенте Azure Active Directory с помощью кода авторизации.
* [Общие сведения о неявном потоке предоставления OAuth2 в Azure Active Directory (AD).](active-directory-dev-understanding-oauth2-implicit-grant.md) Сведения о неявном предоставлении авторизации и его совместимости с приложением.
* [Служба обслуживания вызовов с помощью учетных данных клиента.](active-directory-protocols-oauth-service-to-service.md) Процесс предоставления учетных данных клиента OAuth 2.0 позволяет веб-службе (конфиденциальный клиент) вместо олицетворения пользователя использовать свои собственные учетные данные для проверки подлинности при вызове другой веб-службы. В этом сценарии клиент обычно является веб-службой среднего уровня, службой управляющей программы или веб-сайтом.
* [Предоставление доступа к веб-приложениям с помощью OpenID Connect и Azure Active Directory.](active-directory-protocols-openid-connect-code.md) Протокол OpenID Connect 1.0 дополняет протокол OAuth 2.0, позволяя использовать его для проверки подлинности. Клиентское приложение может получать id_token, чтобы управлять процессом входа в систему или расширять поток кода авторизации для получения кода авторизации и id_token.
* [Справочник по протоколу SAML 2.0](active-directory-saml-protocol-reference.md): протокол SAML 2.0 позволяет приложениям предоставлять пользователям единый вход.
* [WS-Federation 1.2 protocol](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) (Протокол WS-Federation 1.2). Azure Active Directory поддерживает протокол WS-Federation 1.2, как указано в спецификации федерации веб-служб версии 1.2. Дополнительные сведения о документе метаданных федерации см. в статье [Метаданные федерации](active-directory-federation-metadata.md).
* [Справочник по токенам в Azure AD.](active-directory-token-and-claims.md) В этом руководстве вы найдете основные сведения об утверждениях в маркерах SAML 2.0 и веб-маркерах JSON (JWT).

## <a name="videos"></a>Видеоролики
### <a name="build"></a>Создание
В этих обзорных презентациях представители разработчиков освещают ключевые темы, связанные с разработкой приложений с помощью Azure Active Directory, включая IDMaaS, проверку подлинности, федерацию удостоверений и единый вход.

* [Microsoft Identity: State of the Union and Future Direction (Microsoft Identity: последние достижения и перспективы развития)](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: "управление удостоверениями как служба" для современных приложений](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Разработка современных веб-приложений с помощью Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Разработка современных приложений в машинном коде с помощью Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure, пятница
[Пятница с Azure](https://azure.microsoft.com/documentation/videos/azure-friday/) — это еженедельная серия коротких (10–15 минут) видеоинтервью со специалистами на разные темы, связанные с Azure.  Используйте функцию фильтра служб на странице, чтобы просмотреть все видео Azure Active Directory.

* [Удостоверение Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Удостоверение Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Удостоверение Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Социальные сети
* [Блог группы Active Directory.](http://blogs.technet.com/b/ad/) Сведения о последних разработках в сфере Azure Active Directory.
* [Блог группы Azure Active Directory Graph.](https://blogs.msdn.microsoft.com/aadgraphteam) Информация об Azure Active Directory, связанная с API Graph.
* [Облачная идентификация.](http://www.cloudidentity.net) Соображения об управлении удостоверениями от главного руководителя программы Azure Active Directory.  
* [Azure Active Directory в Twitter.](https://twitter.com/azuread) Объявления Azure Active Directory длиной не более 140 символов.

## <a name="windows-server-on-premises-development"></a>Локальная разработка Windows Server
Рекомендации по разработке Windows Server и служб федерации Active Directory (ADFS):

* [Сценарии AD FS для разработчиков](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): обзор компонентов и функций служб федерации Active Directory, а также подробные сведения о поддерживаемых сценариях проверки подлинности и авторизации.
* [Руководства по использованию AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): список руководств с пошаговыми инструкциями по реализации связанных последовательностей проверки подлинности и авторизации.

