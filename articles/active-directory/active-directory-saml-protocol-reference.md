---
title: Справочник по протоколу SAML в Azure AD | Документация Майкрософт
description: В этой статье содержится обзор профилей SAML для единого входа и единого выхода в Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.openlocfilehash: 1fcc854362428d4ae32e815ccbf611f4562399ad
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26734127"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Как Azure Active Directory использует протокол SAML
Azure Active Directory (Azure AD) использует протокол SAML 2.0, чтобы приложения могли предоставлять пользователям единый вход. Профили [единого входа](active-directory-single-sign-on-protocol-reference.md) и [единого выхода](active-directory-single-sign-out-protocol-reference.md) SAML в Azure AD содержат сведения о том, как в службе поставщика удостоверений используются утверждения SAML, протоколы и привязки.

Протокол SAML подразумевает, что поставщик удостоверений (Azure AD) и поставщик услуг (приложение) обмениваются информацией друг о друге.

При регистрации приложения в Azure AD разработчик приложения регистрирует в Azure AD сведения, относящиеся к федерации. К ним, в частности, относится **URI перенаправления** для приложения.

Azure Active Directory предоставляет клиентские и общие (единые для всех клиентов) конечные точки единого входа и единого выхода. Эти URL-адреса представляют собой адресуемые расположения, а не просто идентификаторы. Вы можете обратиться по ним к конечной точке для чтения метаданных.

* Конечная точка конкретного клиента находится по адресу `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  Заполнитель <TenantDomainName> обозначает зарегистрированное доменное имя или глобальный уникальный идентификатор клиента для клиента Azure AD. Например, метаданные федерации для клиента contoso.com будут расположены по адресу https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml.

* Общая для всех клиентов конечная точка находится по адресу `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. В этом адресе конечной точки вместо доменного имени или идентификатора клиента содержится ключевое слово **common**.

Сведения о документах метаданных федерации, публикуемых в Azure AD, см. в статье [Метаданные федерации](active-directory-federation-metadata.md).
