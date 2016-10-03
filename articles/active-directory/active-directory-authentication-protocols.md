<properties
   pageTitle="Протоколы аутентификации Azure Active Directory | Microsoft Azure"
   description="Обзор протоколов проверки подлинности, поддерживаемых в Azure Active Directory (AD)"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# Протоколы проверки подлинности Azure Active Directory

Служба Azure Active Directory (Azure AD) поддерживает несколько широко используемых протоколов проверки подлинности и авторизации. В перечисленных ниже разделах мы рассказываем о поддерживаемых протоколах и их реализации в Azure AD. В эти разделы вошли обзор поддерживаемых типов утверждений, основные сведения об использовании метаданных федерации, подробная документация по протоколам OAuth 2.0 и SAML 2.0, а также советы по устранению неполадок.

## Статьи и другие справочные материалы по протоколам проверки подлинности

- [Важные сведения об откате ключа подписи в Azure AD](active-directory-signing-key-rollover.md) — дополнительные сведения об откате ключа подписи Azure AD, изменениях, которые можно вносить для автоматического обновления ключа, и порядок обновления самых распространенных сценариев приложений.


- [Поддерживаемые типы маркеров и утверждений](active-directory-token-and-claims.md) — сведения об утверждениях в маркерах, издаваемых Azure AD.


- [Метаданные федерации](https://msdn.microsoft.com/library/azure/dn195592.aspx) — узнайте, как находить и интерпретировать документы метаданных, создаваемые Azure AD.


- [OAuth 2.0 в Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) — сведения о реализации протокола OAuth 2.0 в Azure AD.


- [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) — сведения об использовании протокола авторизации OAuth 2.0 для проверки подлинности.


- [Справочник по протоколу SAML](https://msdn.microsoft.com/library/azure/dn195591.aspx) — сведения о профилях единого входа и единого выхода SAML в Azure AD.


- [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) — сведения о WS-Federation 1.2 в Azure AD.


- [Устранение неполадок протоколов проверки подлинности](https://msdn.microsoft.com/library/azure/dn195584.aspx) — узнайте, как предотвратить возникновение проблем, интерпретировать и устранять ошибки при использовании Azure AD.



## См. также

[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

[Использование Azure AD для проверки подлинности](../app-service-web/web-sites-authentication-authorization.md)

[Примеры кода Active Directory](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->