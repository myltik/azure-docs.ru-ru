---
title: "Получение сертификата AppSource для Azure Active Directory | Документация Майкрософт"
description: "Сведения о получении сертификата AppSource приложения для Azure Active Directory."
services: active-directory
documentationcenter: 
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: e1299c1f7f8a31f7034fc0736fcd9d66153a9758
ms.openlocfilehash: 3290a375963bc3e625cbdb05b5f9686e8cfb34f6
ms.contentlocale: ru-ru
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Как получить сертификат AppSource для Azure Active Directory (AD)
Чтобы получить сертификат AppSource для Azure AD, в приложении необходимо реализовать мультитенантный вход с помощью Azure AD с использованием протоколов OpenID Connect или OAuth 2.0.  

Если вы не знакомы с процедурой входа в Azure AD или с разработкой мультитенантных приложений:

1. Начните с изучения [раздела "Из веб-браузера в веб-приложение" статьи "Сценарии аутентификации в Azure Active Directory"][AAD-Auth-Scenarios-Browser-To-WebApp].  
2. Затем ознакомьтесь с [краткими руководствами по началу работы с веб-приложениями][AAD-QuickStart-Web-Apps] для Azure AD, в которых показано, как реализовывать вход и включать сопутствующие примеры кода.

   > [!TIP]
   > Воспользуйтесь предварительной версией нашего нового [портала разработчиков](https://identity.microsoft.com/Docs/Web) , который поможет вам приступить к работе с Azure Active Directory через несколько минут.  Портал разработчиков поможет зарегистрировать приложение и интегрировать Azure AD в коде.  Завершив работу, вы получите простое приложение, с помощью которого выполняется проверка подлинности пользователей в клиенте и на сервере, принимающем маркеры и проводящем проверку.
   >
   >
3. Чтобы узнать, как реализовать мультитенантный вход с помощью Azure AD, см. статью [Как реализовать вход любого пользователя Azure Active Directory (AD) с помощью шаблона мультитенантного приложения][AAD-Howto-Multitenant-Overview].

## <a name="related-content"></a>Связанная информация
Для получения дополнительных сведений о создании приложений с поддержкой входа в Azure AD или справочной информации и технической поддержки см. [руководство разработчика по Azure Active Directory][AAD-Dev-Guide].

Оставляйте свои замечания и пожелания в разделе DISQUS ниже. Они помогают нам улучшать содержимое веб-сайта.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->

