---
title: "Протоколы проверки подлинности Azure Active Directory | Документация Майкрософт"
description: "Обзор протоколов проверки подлинности, поддерживаемых в Azure Active Directory (AD)"
documentationcenter: dev-center-name
author: bryanla
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0b035ad1505e45c8c0820c825ff609df6e6100f0
ms.openlocfilehash: 20642b0c1864cede5326e98f6ce3145314acc49a


---
# <a name="azure-active-directory-authentication-protocols"></a>Протоколы проверки подлинности Azure Active Directory
Служба Azure Active Directory (Azure AD) поддерживает несколько широко используемых протоколов проверки подлинности и авторизации. В перечисленных ниже разделах мы рассказываем о поддерживаемых протоколах и их реализации в Azure AD. Эти разделы содержат обзор поддерживаемых типов утверждений, основные сведения об использовании метаданных федерации, подробную документацию по протоколам OAuth 2.0. и SAML 2.0, а также советы по устранению неполадок.

## <a name="authentication-protocols-articles-and-reference"></a>Статьи и другие справочные материалы по протоколам проверки подлинности
* [Важные сведения об откате ключа подписи в Azure AD](active-directory-signing-key-rollover.md) — дополнительные сведения об откате ключа подписи Azure AD, изменениях, которые можно вносить для автоматического обновления ключа, и порядок обновления самых распространенных сценариев приложений.
* [Поддерживаемые типы маркеров и утверждений](active-directory-token-and-claims.md) — сведения об утверждениях в маркерах, издаваемых Azure AD.
* [Метаданные федерации](https://msdn.microsoft.com/library/azure/dn195592.aspx) — узнайте, как находить и интерпретировать документы метаданных, создаваемые Azure AD.
* [OAuth 2.0 в Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) — сведения о реализации протокола OAuth 2.0 в Azure AD.
* [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) — сведения об использовании протокола авторизации OAuth 2.0 для проверки подлинности.
* [Справочник по протоколу SAML](https://msdn.microsoft.com/library/azure/dn195591.aspx) — сведения о профилях единого входа и единого выхода SAML в Azure AD.
* [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) — сведения о WS-Federation 1.2 в Azure AD.
* [Устранение неполадок протоколов проверки подлинности](https://msdn.microsoft.com/library/azure/dn195584.aspx) — узнайте, как предотвратить возникновение проблем, интерпретировать и устранять ошибки при использовании Azure AD.

## <a name="see-also"></a>См. также
[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

[Использование Azure AD для проверки подлинности](../../app-service-web/web-sites-authentication-authorization.md)

[Примеры кода Active Directory](active-directory-code-samples.md)




<!--HONumber=Jan17_HO3-->


