<properties
	pageTitle="Протоколы проверки подлинности Active Directory | Microsoft Azure"
	description="В этой статье содержится обзор различных протоколов проверки подлинности и авторизации, которые поддерживает Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/06/2016"
	ms.author="priyamo"/>

# Протоколы проверки подлинности Active Directory

Служба Azure Active Directory (Azure AD) поддерживает несколько широко используемых протоколов проверки подлинности и авторизации.

В этом цикле статей мы рассмотрим поддерживаемые протоколы и способы их реализации в Azure AD. Мы приведем примеры запросов и ответов. Так как интеграция выполняется непосредственно с использованием протоколов, инструкции в этих статьях преимущественно не зависят от языка.

- [OAuth 2.0 authorization code grant](active-directory-protocols-oauth-code.md) (Предоставление кода авторизации OAuth 2.0): сведения о процессе предоставления "кода авторизации" OAuth2.0 и его реализации в Azure AD.
- [OAuth 2.0 implicit grant](active-directory-dev-understanding-oauth2-implicit-grant.md) (Неявное предоставление авторизации OAuth 2.0): сведения о процессе неявного предоставления авторизации OAuth 2.0 и его совместимости с вашим приложением.
- [Open ID Connect 1.0](active-directory-protocols-openid-connect-code.md): сведения об использовании протокола проверки подлинности OpenID Connect в Azure AD.
- [Справочник по протоколу SAML](active-directory-saml-protocol-reference.md): сведения об использовании протокола SAML для поддержки [единого входа](active-directory-single-sign-on-protocol-reference.md) и [единого выхода](active-directory-single-sign-out-protocol-reference.md) в Azure AD.


## Ссылки и устранение неполадок

В этих статьях вы найдете дополнительную информацию, которая поможет вам не только эффективно устранять неполадки с приложениями Azure AD, но и узнать больше об Azure AD.

- [Метаданные федерации](active-directory-federation-metadata.md): сведения о том, как находить и интерпретировать документы метаданных, создаваемые Azure AD.
- [Поддерживаемые токены и типы утверждений](active-directory-token-and-claims.md): сведения о различных утверждениях в токенах, издаваемых Azure AD.
- [Signing Key Rollover in Azure AD](active-directory-signing-key-rollover.md) (Смена ключей подписывания в Azure AD): сведения о смене ключей подписывания в Azure AD и о том, как обновлять ключи для самых распространенных сценариев приложений.
- [Troubleshooting Authentication Protocols](active-directory-error-handling.md) (Устранение неполадок протоколов проверки подлинности): сведения об интерпретировании и устранении наиболее распространенных ошибок при использовании OAuth 2.0 и Azure AD.
- [Советы и рекомендации по OAuth 2.0 в Azure AD](active-directory-oauth-best-practices.md): ознакомьтесь с рекомендациями по использованию OAuth 2.0 в Azure AD, чтобы избежать распространенных проблем.

<!---HONumber=AcomDC_0615_2016-->