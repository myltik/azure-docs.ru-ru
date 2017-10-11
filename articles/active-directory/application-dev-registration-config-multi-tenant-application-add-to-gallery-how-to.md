---
title: "Добавление мультитенантного приложения в коллекцию приложений Azure AD | Документы Майкрософт"
description: "В этой статье объясняется, как включить специально разработанное мультитенантное приложение в коллекцию приложений Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 208f0d40bd7a8e8f35f16e1fcb09c305d833dbb2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Добавление мультитенантного приложения в коллекцию приложений Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Что такое коллекция приложений Azure AD?

Коллекция приложений Azure AD является отличным способом представить приложение миллионам пользователей Azure Active Directory, что увеличивает сферу его влияния и позволяет расширить аудиторию приложения в магазине. Ниже показано, как добавить приложение в коллекцию приложений Azure AD.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Если приложение поддерживает SAML или OpenIDConnect
Если у вас есть многопользовательское приложение, которые вы хотите включить в коллекцию приложений Azure AD, сначала убедитесь, что оно поддерживает одну из следующих технологий единого входа:

1. **OpenID Connect.** Прямая интеграция с Azure AD с помощью OpenID Connect для проверки подлинности и API согласия Azure AD для конфигурации. Этот режим является рекомендуемым, если вы только начали интеграцию и ваше приложение не поддерживает SAML.
2. **SAML.** Приложение уже имеет возможность настройки сторонних поставщиков удостоверений с помощью протокола SAML.

Если ваше мультитенантное приложение поддерживает один из этих режимов единого входа и вы бы хотели включить его в коллекцию приложений Azure AD, выполните действия, описанные в документе ниже. Чтобы быстро начать, отправьте электронное сообщение по адресу **waadpartners@microsoft.com**.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Если приложение не поддерживает SAML или OpenIDConnect
Даже если приложение не поддерживает один из этих режимов, мы по-прежнему можем интегрировать его в свою коллекцию, применив технологию единого входа по паролю. Если вы хотите изучить эту возможность, отправьте электронное сообщение по адресу **waadpartners@microsoft.com**.

## <a name="next-steps"></a>Дальнейшие действия
[Добавление приложения в коллекцию приложений Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
