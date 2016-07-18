<properties
   pageTitle="Как получить сертификат AppSource для Azure Active Directory| Microsoft Azure"
   description="Сведения о получении сертификата AppSource приложения для Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/05/2016"
   ms.author="skwan;bryanla"/>

#Как получить сертификат AppSource для Azure Active Directory (AD) 

Чтобы получить сертификат AppSource для Azure AD, в приложении необходимо реализовать мультитенантный вход с помощью Azure AD с использованием протоколов OpenID Connect, OAuth 2.0 или SAML 2.0.

Если вы не знакомы с процедурой входа в Azure AD или с разработкой мультитенантных приложений:

1. Начните с изучения [раздела "Из веб-браузера в веб-приложение" статьи "Сценарии аутентификации в Azure Active Directory"][AAD-Auth-Scenarios-Browser-To-WebApp].
2. Затем ознакомьтесь с [краткими руководствами по началу работы с веб-приложениями][AAD-QuickStart-Web-Apps] для Azure AD, в которых показано, как реализовывать вход и включать сопутствующие примеры кода.
3. Чтобы узнать, как реализовать мультитенантный вход с помощью Azure AD, см. статью [How to sign in any Azure Active Directory (AD) user using the multi-tenant application pattern][AAD-Howto-Multitenant-Overview] (Как реализовать вход любого пользователя Azure Active Directory (AD) с помощью шаблона мультитенантного приложения).

## Связанная информация
Для получения дополнительных сведений о создании приложений с поддержкой входа Azure AD или справочной информации и технической поддержки см. [Руководство разработчика по Azure Active Directory][AAD-Dev-Guide].

Оставляйте свои замечания и пожелания в разделе DISQUS ниже. Они помогают нам улучшать содержимое веб-сайта.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->

<!---HONumber=AcomDC_0706_2016-->