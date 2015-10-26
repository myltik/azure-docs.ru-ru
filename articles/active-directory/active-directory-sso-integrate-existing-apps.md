<properties
   pageTitle="Интеграция единого входа Azure Active Directory с существующими приложениями | Microsoft Azure"
   description="Управляйте имеющимися приложениями SaaS, реализовав единый вход и подготовку пользователей в Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# Интеграция единого входа Azure Active Directory с существующими приложениями

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## Дополнительные рекомендации

Настройка единого входа для тех приложений, которые вы уже используете, отличается от процесса создания новых учетных записей в новых приложениях. При интеграции приложения в Azure AD изменения будут заметны как администраторам, так и рядовым пользователям.

### Что необходимо знать администраторам, чтобы настроить единый вход для уже используемых приложений?

Чтобы настроить единый вход для существующего приложения, необходимо иметь права глобального администратора как в Azure AD, так и в приложении SaaS.

Поскольку данное приложение уже используется, потребуется связать имеющиеся в приложении удостоверения пользователей с соответствующими им удостоверениями в Azure AD. Важно знать, что именно это приложение использует в качестве уникального идентификатора для входа, например адрес электронной почты, универсальное имя (UPN) или имя пользователя. Это значение будет связано с уникальным идентификатором пользователя в Azure AD. Дополнительные сведения о связывании удостоверений приложения с удостоверениями Azure AD см. в разделах [Настройка утверждений, выпущенных в токене SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) и [Настройка сопоставления атрибутов для подготовки](active-directory-saas-customizing-attribute-mappings.md).

### Как это повлияет на конечных пользователей?

При интеграции единого входа для уже используемого приложения важно понимать, что это повлияет на работу пользователей. Во всех приложениях пользователи начнут использовать для входа свои учетные данные Azure AD. Кроме того, им может потребоваться использовать другой портал для доступа к приложениям. Для некоторых приложений единый вход может осуществляться на веб-сайте такого приложения, а для других приложений пользователю потребуется воспользоваться центральным порталом приложений ([Мои приложения](myapps.microsoft.com) или [Office365](portal.office.com/myapps)). Дополнительные сведения о различных типах единого входа и о соответствующем взаимодействии с пользователем см. в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Дальнейшие действия
- Узнайте, как [интегрировать единый вход Azure Active Directory с новыми приложениями](active-directory-sso-newly-acquired-saas-apps.md).
- Узнайте подробнее о [доступе к приложениям и едином входе с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).
- Найдите[руководства по интеграции приложений SaaS](active-directory-saas-tutorial-list.md).
-	Добавьте пользовательское приложение с помощью [конфигурации SAML в компоненте самообслуживания Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

<!---HONumber=Oct15_HO3-->