---
title: "Протоколы проверки подлинности Azure Active Directory | Документация Майкрософт"
description: "Обзор протоколов проверки подлинности, поддерживаемых в Azure Active Directory (AD)"
documentationcenter: dev-center-name
author: priyamohanram
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: priyamo
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 27c582436bd57adfb5e8edb0f63c0f206db66c89
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-authentication-protocols"></a>Протоколы проверки подлинности Azure Active Directory
Служба Azure Active Directory (Azure AD) поддерживает несколько широко используемых протоколов проверки подлинности и авторизации. В перечисленных ниже разделах мы рассказываем о поддерживаемых протоколах и их реализации в Azure AD. Эти разделы содержат обзор поддерживаемых типов утверждений, основные сведения об использовании метаданных федерации, подробную документацию по протоколам OAuth 2.0. и SAML 2.0, а также советы по устранению неполадок.

## <a name="authentication-protocols-articles-and-reference"></a>Статьи и другие справочные материалы по протоколам проверки подлинности
* [Важные сведения об откате ключа подписи в Azure AD](active-directory-signing-key-rollover.md) — дополнительные сведения об откате ключа подписи Azure AD, изменениях, которые можно вносить для автоматического обновления ключа, и порядок обновления самых распространенных сценариев приложений.
* [Справочник по токенам в Azure AD](active-directory-token-and-claims.md) — сведения об утверждениях в токенах, издаваемых Azure AD.
* [Метаданные федерации](active-directory-federation-metadata.md) — узнайте, как находить и интерпретировать документы метаданных, создаваемые Azure AD.
* [OAuth 2.0 в Azure AD](active-directory-protocols-oauth-code.md) — сведения о реализации протокола OAuth 2.0 в Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) — сведения об использовании протокола авторизации OAuth 2.0 для проверки подлинности.
* [Служба обслуживания вызовов с помощью учетных данных клиента](active-directory-protocols-oauth-service-to-service.md) — сведения об использовании потока предоставления учетных данных клиента OAuth 2.0 для вызовов между службами.
* [Служба обслуживания вызовов с помощью потока On-Behalf-Of](active-directory-protocols-oauth-on-behalf-of.md) — сведения об использовании потока On-Behalf-Of OAuth 2.0 для вызовов между службами.
* [Справочник по протоколу SAML](active-directory-saml-protocol-reference.md) — сведения о профилях единого входа и единого выхода SAML в Azure AD.

## <a name="see-also"></a>См. также
[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

[Использование Azure AD для проверки подлинности](../../app-service-web/web-sites-authentication-authorization.md)

[Примеры кода Active Directory](active-directory-code-samples.md)

