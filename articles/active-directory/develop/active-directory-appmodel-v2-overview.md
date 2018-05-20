---
title: Конечная точка Azure Active Directory версии 2.0 | Документация Майкрософт
description: Общие сведения о создании приложений с поддержкой входа, обеспечиваемой использованием учетной записи Майкрософт и Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0fde3cb77389994352b62f06d370c709fa115749
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Настройка входа пользователей с помощью учетной записи Майкрософт и Azure Active Directory в одном приложении
Раньше разработчикам приложений, чтобы реализовать поддержку как личных учетных записей Майкрософт, так и рабочих учетных записей Azure Active Directory, необходимо было обеспечивать интеграцию с двумя отдельными системами. Конечная точка Azure Active Directory (Azure AD) версии 2.0 предлагает новую версию API аутентификации, которая упрощает этот процесс. Конечная точка Azure AD 2.0 обеспечивает вход из обоих типов учетных записей с применением интеграции. Приложения, использующие конечную точку Azure AD 2.0, также могут использовать интерфейсы REST API из [API Microsoft Graph](https://graph.microsoft.io) с помощью учетной записи любого из этих типов.

## <a name="getting-started"></a>Приступая к работе
Выберите платформу в следующем списке, чтобы создать приложение, используя платформы и библиотеки Майкрософт с открытым исходным кодом. Также можно использовать протоколы OAuth 2.0 и OpenID Connect для отправки и получения сообщений протокола напрямую без использования библиотеки аутентификации.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Дополнительные сведения о конечной точке Azure AD версии 2.0
Изучите возможные действия с конечной точкой Azure AD 2.0:

* Узнайте о [типах приложений, которые можно создавать с помощью конечной точки Azure AD 2.0](active-directory-v2-flows.md).
* Ознакомьтесь с [ограничениями](active-directory-v2-limitations.md) конечной точки версии 2.0.
* Просмотрите обзорное видео о конечной точке Azure AD версии 2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Дополнительные ресурсы
Узнайте больше о платформе конечной точки Azure AD версии 2.0:

* [Справочник по протоколам Azure AD 2.0](active-directory-v2-protocols.md)
* [Справочник по токенам Azure AD 2.0](active-directory-v2-tokens.md)
* [Справочник по библиотекам проверки подлинности Azure AD 2.0](active-directory-v2-libraries.md)
* [Области действия и разрешения в конечной точке Azure AD 2.0](active-directory-v2-scopes.md)
* [API-интерфейс Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Если вам нужно использовать вход только с рабочими или учебными учетными записями из Azure Active Directory, начните изучение с [руководства разработчика Azure AD](active-directory-developers-guide.md). Конечная точка Azure AD версии 2.0 предназначена для разработчиков, которым необходимо выполнять вход с использованием личных учетных записей Майкрософт.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
