---
title: Добавление мультитенантного приложения в коллекцию приложений Azure AD | Документация Майкрософт
description: В этой статье объясняется, как включить специально разработанное мультитенантное приложение в коллекцию приложений Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: 51a088ccdcc018b85a70f72a5f88fab8de3c7363
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150878"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Добавление мультитенантного приложения в коллекцию приложений Azure Active Directory

## <a name="what-is-the-azure-ad-application-gallery"></a>Что такое коллекция приложений Azure AD?

Azure Active Directory (Azure AD) — это облачная служба идентификации. [Коллекция приложений Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) представляет собой хранилище приложений Azure Marketplace, в котором публикуются все соединители приложений для единого входа и подготовки пользователей. Клиенты, которые используют Azure AD в качестве поставщика удостоверений, находят опубликованные здесь различные соединители приложений SaaS. Администраторы ИТ-систем добавляют соединители из коллекции приложений, а затем настраивают и используют эти соединители для единого входа и подготовки. Azure Active Directory поддерживает для функции единого входа все основные протоколы федерации, в том числе SAML 2.0, OpenID Connect, OAuth и WS-Fed. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Если приложение поддерживает SAML или OpenIDConnect
Если у вас есть многопользовательское приложение, которое вы хотите включить в коллекцию приложений Azure Active Directory, сначала убедитесь, что оно поддерживает одну из следующих технологий единого входа:

- **OpenID Connect**. Чтобы приложение отображалось здесь, создайте многопользовательское приложение в Azure Active Directory и реализуйте в нем [инфраструктуру согласия Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework). Направляйте запрос на вход к общедоступной конечной точке, чтобы любой клиент мог предоставить согласие для приложения. Доступом пользователей можно управлять на основе идентификатора клиента и имени участника-пользователя, которые передаются в маркере. Отправьте приложение, следуя инструкциям в статье [Добавление приложения в коллекцию приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML.** Если приложение поддерживает SAML 2.0, оно может отображаться в коллекции. Следуйте инструкциям в статье [Добавление приложения в коллекцию приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Если приложение не поддерживает SAML или OpenIDConnect
Приложения, которые не поддерживают SAML и OpenIDConnect, все равно можно интегрировать в коллекцию приложений, используя технологию единого входа на основе пароля.

Единый вход на основе пароля (также называется хранение пароля) позволяет управлять доступом и паролями для веб-приложений, которые не поддерживают федерацию удостоверений. Также он полезен в ситуациях, когда несколько пользователей работают под одной учетной записью (например, с корпоративными страницами в социальных сетях). 

Чтобы ваше приложение поддерживало эту технологию, сделайте следующее:
1. Создайте веб-приложение с HTML-страницей единого входа, чтобы [настроить единый вход на основе пароля](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Оправьте запрос, следуя инструкциям в статье [Добавление приложения в коллекцию приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Эскалация вопросов

При эскалации любых вопросов отправьте электронное письмо [команде интеграции единого входа Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Мы ответим вам как можно скорее.

## <a name="next-steps"></a>Дополнительная информация
Узнайте, как [добавить приложение в коллекцию приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
