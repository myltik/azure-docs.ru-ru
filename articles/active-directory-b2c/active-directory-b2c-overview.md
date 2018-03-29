---
title: Azure Active Directory B2C. Веб-приложения и мобильные приложения для управления удостоверениями в облаке | Документация Майкрософт
description: Разработка потребительских приложений с помощью Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: 6385a4e4b89cf889b71db6e09010dfe3a674336a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-focus-on-your-app-let-us-worry-about-sign-up-and-sign-in"></a>Настройка входа и регистрация приложения в Azure AD B2C

Azure AD B2C — это облачное решение, позволяющее управлять удостоверениями в веб-приложениях и мобильных приложениях. Это высокодоступная глобальная служба, которая масштабируется до сотен миллионов удостоверений. Так как служба Azure AD B2C разработана на основе безопасной платформы корпоративного уровня, она защищает приложения, бизнес-процессы и пользовательские данные.

С минимальной конфигурацией Azure AD B2C позволяет приложению выполнить аутентификацию следующих ресурсов:

* **учетных записей социальных сетей** (например, Facebook, Google, LinkedIn и другое);
* **корпоративных учетных записей** (с помощью открытых стандартных протоколов, OpenID Connect или SAML);
* **локальных учетных записей** (адрес электронной почты и пароль или имя пользователя и пароль).

## <a name="get-started"></a>Начало работы

Сначала получите собственный клиент, следуя указаниям в статье о [создании клиента Azure AD B2C](active-directory-b2c-get-started.md).

Затем выберите сценарий разработки приложения:

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Классические и мобильные приложения](../active-directory/develop/media/active-directory-developers-guide/NativeApp_Icon.png)<br />Классические и мобильные приложения</center> | [Azure Active Directory B2C: поток кода авторизации OAuth 2.0](active-directory-b2c-reference-oauth-code.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<br /><br />[iOS](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)<br /><br />[Android](https://github.com/Azure-Samples/active-directory-b2c-android-native-msal) | [.NET](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)<br /><br />[Xamarin](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) |  |
| <center>![Веб-приложения](../active-directory/develop/media/active-directory-developers-guide/Web_app.png)<br />Веб-приложения</center> | [Обзор](active-directory-b2c-reference-oidc.md)<br /><br />[ASP.NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)<br /><br />[ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapp) | [Node.js](active-directory-b2c-devquickstarts-web-node.md) |  |
| <center>![Одностраничные приложения](../active-directory/develop/media/active-directory-developers-guide/SPA.png)<br />Одностраничные приложения</center> | [Обзор](active-directory-b2c-reference-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)<br /><br /> |  |  |
| <center>![Веб-интерфейсы API](../active-directory/develop/media/active-directory-developers-guide/Web_API.png)<br />Веб-интерфейсы API</center> | [ASP.NET](active-directory-b2c-devquickstarts-api-dotnet.md)<br /><br /> [ASP.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)<br /><br /> [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | [Вызов веб-API .NET](active-directory-b2c-devquickstarts-web-api-dotnet.md) |

## <a name="whats-new"></a>Новые возможности

Регулярно просматривайте указанные ниже страницы и узнавайте о будущих изменениях в Azure Active Directory B2C. Кроме того, мы будем сообщать о нововведениях в Twitter под именем @AzureAD.

* В дополнение к общедоступной версии встроенных политик [пользовательские политики](active-directory-b2c-overview-custom.md) предоставляются в общедоступной предварительной версии.  Пользовательские политики предназначены для специалистов, которым необходимо контролировать структуру процедур идентификации.
* Функция [маркера доступа](https://azure.microsoft.com/en-us/blog/azure-ad-b2c-access-tokens-now-in-public-preview) теперь общедоступна в предварительной версии.
* Объявлено о выпуске каталогов [общедоступной версии Azure AD B2C в Европе](https://azure.microsoft.com/en-us/blog/azuread-b2c-ga-eu/).
* Ознакомьтесь с постоянно растущей коллекцией [примеров кода на Github](https://github.com/Azure-Samples?q=b2c).

## <a name="how-to-articles"></a>Статьи с инструкциями

Узнайте, как использовать отдельные функции Azure Active Directory B2C:

* Настройка учетных записей [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Microsoft account](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) и [LinkedIn](active-directory-b2c-setup-li-app.md) для использования в приложениях, ориентированных на потребителей.
* [Используйте настраиваемые атрибуты для сбора данных о потребителях](active-directory-b2c-reference-custom-attr.md).
* [Предварительная версия Azure Active Directory B2C: включение многофакторной проверки подлинности в пользовательских приложениях](active-directory-b2c-reference-mfa.md).
* [Предварительная версия Azure Active Directory B2C: настройка самостоятельного сброса пароля для потребителей](active-directory-b2c-reference-sspr.md).
* [Настройка красивого и удобного интерфейса для регистрации и входа, а также других страниц, с которыми взаимодействуют клиенты](active-directory-b2c-reference-ui-customization.md), с использованием Azure Active Directory B2C.
* [Использование API Graph Azure Active Directory для программного создания, чтения, обновления и удаления пользователей](active-directory-b2c-devquickstarts-graph-dotnet.md) в клиенте Azure Active Directory B2C.

## <a name="next-steps"></a>Дополнительная информация

Следующие ссылки позволят глубже изучить службу:

* См. страницу [цен на Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Ознакомьтесь с [примерами кода](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) для Azure Active Directory B2C. 
* См. материалы на сайте Stack Overflow (Переполнение стека) по тегу [azure-ad-b2c](http://stackoverflow.com/questions/tagged/azure-ad-b2c).
* Сообщите нам свое мнение через сайт [пользовательских мнений](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
* См. статью [Azure AD B2C: протоколы проверки подлинности](active-directory-b2c-reference-protocols.md).
* См. статью [Azure AD B2C: справочник по маркерам](active-directory-b2c-reference-tokens.md).
* См. статью [Azure Active Directory B2C: часто задаваемые вопросы](active-directory-b2c-faqs.md).
* [Предварительная версия Azure Active Directory B2C: регистрация запросов в службу поддержки](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

