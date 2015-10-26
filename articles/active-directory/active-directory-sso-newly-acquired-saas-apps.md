<properties
   pageTitle="Интеграция единого входа Azure Active Directory с новыми приложениями | Microsoft Azure"
   description="Azure Active Directory поддерживает единый вход для новых приложений SaaS для реализации централизованного управления доступом на портале Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/09/2015"
      ms.author="curtand"/>

# Интеграция единого входа Azure Active Directory с новыми приложениями  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Чтобы приступить к настройке единого входа для приложения, которое впервые используется в вашей организации, вы будете использовать существующий каталог в Azure Active Directory. Можно использовать каталог Azure AD, полученный через Microsoft Azure, Office 365 или Windows Intune. При наличии двух или более из этих служб обратитесь к разделу [Администрирование каталога Azure AD](active-directory-administer.md), чтобы определить, какую службу следует использовать.

## Дополнительные рекомендации

### Аутентификация

Для приложений, которые поддерживают протоколы SAML 2.0, WS-Federation или OpenID Connect, Azure Active Directory использует сертификаты подписи для установления отношений доверия. Дополнительные сведения об этом см. в разделе [Управление сертификатами для федеративного единого входа](active-directory-sso-certs.md).

Для приложений, поддерживающих только вход на основе форм HTML, Azure Active Directory использует "хранилище паролей" для установления отношений доверия. Это позволяет пользователям вашей организации автоматически входить в приложение SaaS с помощью Azure AD, используя сведения учетной записи пользователя из приложения SaaS. Azure AD собирает и безопасно хранит данные учетной записи пользователя и связанный с ней пароль. Дополнительные сведения см. в разделе [Единый вход на основе пароля](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

### Авторизация

Подготовленная учетная запись дает возможность авторизоваться для использования приложения после проверки подлинности посредством единого входа. Подготовку пользователей можно выполнять вручную, а в отдельных случаях можно добавлять и удалять сведения о пользователях в приложении SaaS с учетом изменений, внесенных в Azure Active Directory. Дополнительные сведения об использовании существующих соединителей Azure AD для автоматической подготовки см. в разделе [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS](active-directory-saas-app-provisioning.md).

В противном случае можно вручную добавить сведения о пользователе в приложение, а также использовать другие решения для подготовки, доступные на рынке.

### Access

Azure AD предоставляет несколько настраиваемых способов развертывания приложений для конечных пользователей в организации. Вы не привязаны к конкретному решению развертывания или доступа. Можно использовать то [решение, которое наилучшим образом соответствует вашим потребностям](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## Дальнейшие действия

Для уже используемых в организации приложений SaaS, для которых требуется включить единый вход, см. раздел [Интеграция единого входа Azure Active Directory с существующими приложениями](active-directory-sso-integrate-existing-apps.md).

Для приложений SaaS из коллекции приложений Azure Active Directory предоставляет ряд [руководств по интеграции приложений SaaS](active-directory-saas-tutorial-list.md).

Если приложение отсутствует в коллекции приложений, вы можете [добавить его в коллекцию приложений Azure Active Directory в качестве пользовательского приложения](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

В библиотеке Azure.com представлены более подробные сведения обо всех этих аспектах, начиная с раздела [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!---HONumber=Oct15_HO3-->