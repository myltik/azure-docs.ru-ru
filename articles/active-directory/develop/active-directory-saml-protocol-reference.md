---
title: "Справочник по протоколу SAML в Azure AD | Документация Майкрософт"
description: "В этой статье содержится обзор профилей SAML для единого входа и единого выхода в Azure Active Directory."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: priyamo
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: d5ffba5d0c409fe9de7a9e82c6faa4ca2702ab95
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# Как Azure Active Directory использует протокол SAML
Azure Active Directory (Azure AD) использует протокол SAML 2.0, чтобы приложения могли предоставлять пользователям единый вход. Профили [единого входа](active-directory-single-sign-on-protocol-reference.md) и [единого выхода](active-directory-single-sign-out-protocol-reference.md) SAML в Azure AD содержат сведения о том, как в службе поставщика удостоверений используются утверждения SAML, протоколы и привязки.

Протокол SAML подразумевает, что поставщик удостоверений (Azure AD) и поставщик услуг (приложение) обмениваются информацией друг о друге.

При регистрации приложения в Azure AD разработчик приложения регистрирует в Azure AD сведения, относящиеся к федерации. К ним, в частности, относятся **URI перенаправления** и **URI метаданных** для приложения.

Azure AD использует **URI метаданных** облачной службы, чтобы получить ключ подписывания и URI выхода облачной службы. Если приложение не поддерживает URI метаданных, разработчику следует после регистрации приложения обратиться в службу поддержки Майкрософт, чтобы с ее помощью указать URI выхода и ключ подписывания.

Azure Active Directory предоставляет клиентские и общие (единые для всех клиентов) конечные точки единого входа и единого выхода. Эти URL-адреса представляют собой адресуемые расположения, а не просто идентификаторы. Вы можете обратиться по ним к конечной точке для чтения метаданных.

* Конечная точка конкретного клиента находится по адресу `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  Заполнитель <TenantDomainName> обозначает зарегистрированное доменное имя или глобальный уникальный идентификатор клиента для клиента Azure AD. Например, метаданные федерации для клиента contoso.com будут расположены по адресу https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml.

* Общая для всех клиентов конечная точка находится по адресу `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. В этом адресе конечной точки вместо доменного имени или идентификатора клиента содержится ключевое слово **common**.

Сведения о документах метаданных федерации, публикуемых в Azure AD, см. в статье [Метаданные федерации](active-directory-federation-metadata.md).
