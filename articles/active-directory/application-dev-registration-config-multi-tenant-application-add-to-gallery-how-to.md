---
title: "Добавление мультитенантного приложения в коллекцию приложений Azure AD | Документы Майкрософт"
description: "В этой статье объясняется, как включить специально разработанное мультитенантное приложение в коллекцию приложений Azure AD"
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
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Добавление мультитенантного приложения в коллекцию приложений Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Что такое коллекция приложений Azure AD?

Azure AD — это облачная служба удостоверений. [Коллекция приложений Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) — это общедоступное хранилище, в котором публикуются все соединители приложений для единого входа и подготовки пользователей. Наши общие клиенты, использующие Azure AD в качестве поставщика удостоверений, ищут здесь опубликованные соединители для разных приложений SaaS. Администраторы ИТ-систем добавляют соединители из коллекции приложений, а также настраивают и используют их для единого входа и подготовки. Azure AD поддерживает для функции единого входа все основные протоколы федерации, в том числе SAML 2.0, OpenID Connect, OAuth и WS-Fed. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Если приложение поддерживает SAML или OpenIDConnect
Если у вас есть многопользовательское приложение, которые вы хотите включить в коллекцию приложений Azure AD, сначала убедитесь, что оно поддерживает одну из следующих технологий единого входа:

1. **OpenID Connect**. Создайте многопользовательское приложение в Azure AD и реализуйте в нем [инфраструктуру согласия Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework). Направляйте запрос на вход к общедоступной конечной точке, чтобы любой клиент мог предоставить согласие для приложения. Доступом пользователей клиента можно управлять на основе идентификатора клиента и имени участника-пользователя, которые передаются в маркере. Отправьте заявку, следуя указаниям в [этой статье](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

2. **SAML**. Если приложение поддерживает протокол SAML 2.0, мы можем разместить его в коллекции. [Здесь](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) приведены необходимые инструкции.

Если ваше мультитенантное приложение поддерживает один из этих режимов единого входа и вы бы хотели включить его в коллекцию приложений Azure AD, выполните действия, описанные в [этой](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) статье. 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Если приложение не поддерживает SAML или OpenIDConnect
Даже если приложение не поддерживает один из этих режимов, мы по-прежнему можем интегрировать его в свою коллекцию, применив технологию единого входа по паролю.

**Единый вход с помощью пароля**. Создайте веб-приложение с HTML-страницей единого входа, чтобы [настроить единый вход с помощью пароля](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). Единый вход с помощью пароля (хранение пароля в хранилище) позволяет управлять доступом и паролями для веб-приложений, которые не поддерживают федерацию удостоверений. Также он полезен в ситуациях, когда несколько пользователей работают под одной учетной записью (например, с корпоративными страницами в социальных сетях). 

Если нужно добавить приложение с этой технологией в список, отправьте запрос, следуя указаниям в [этой](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) статье.

## <a name="escalations"></a>Эскалация вопросов

Если возникла проблема, требующая внимания на более высоком уровне, отправьте электронное письмо [команде интеграции единого входа Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>), и мы свяжемся с вами при первой возможности.

## <a name="next-steps"></a>Дополнительная информация
[Добавление приложения в коллекцию приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
