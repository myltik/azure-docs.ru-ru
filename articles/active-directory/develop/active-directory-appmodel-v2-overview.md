---
title: "Конечная точка Azure Active Directory версии 2.0 | Документация Майкрософт"
description: "Общие сведения о создании приложений с поддержкой входа как по учетной записи Майкрософт, так и через Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 4a9bf4193017313f532b398880a84966367f0b58
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении
Раньше разработчику приложения, поддерживающего как личные учетные записи Майкрософт, так и рабочие учетные записи Azure Active Directory, необходимо было обеспечивать интеграцию с двумя отдельными системами.  **Конечная точка Azure AD версии 2.0** представляет новую версию API аутентификации, которая позволяет выполнять вход пользователей с учетными записями обоих типов с помощью простой интеграции.  Приложения, использующие конечную точку версии 2.0, также могут использовать интерфейсы REST API из [Microsoft Graph](https://graph.microsoft.io) с помощью учетной записи любого из этих типов.

## <a name="getting-started"></a>Приступая к работе
Выберите платформу в следующем списке, чтобы создать приложение с помощью наших библиотек и платформ с открытым исходным кодом.  Вы также можете воспользоваться документацией по протоколу OAuth 2.0 и OpenID Connect для отправки и получения сообщений протокола напрямую, без использования библиотеки аутентификации.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Новые возможности
Сведения, приведенные здесь, будут полезны в понимании возможностей конечной точки версии 2.0.

* Узнайте о [типах приложений, которые можно создавать с помощью конечной точки версии 2.0](active-directory-v2-flows.md).
* Ознакомьтесь с [ограничениями](active-directory-v2-limitations.md) конечной точки версии 2.0.
* Ознакомьтесь с видеообзором конечной точки версии 2.0:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Справочные материалы
Следующие ссылки позволят глубже изучить платформу:

* [Справочник по протоколу версии 2.0](active-directory-v2-protocols.md)
* [Справочник по маркерам версии 2.0](active-directory-v2-tokens.md)
* [Справочник по библиотеке версии 2.0](active-directory-v2-libraries.md)
* [Области и разрешения в конечной точке версии 2.0](active-directory-v2-scopes.md)
* [Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>Справка и поддержка
Это лучшие места, чтобы получить помощь в разработке для Azure Active Directory.

* [Stack Overflow`azure-active-directory` и `adal`теги](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Отзывы об Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> Если требуется использовать вход только с рабочими или учебными учетными записями из Azure Active Directory, следует начать изучение с нашего [руководства разработчика Azure AD](active-directory-developers-guide.md).  Конечная точка версии 2.0 предназначена для разработчиков, которым необходимо явно выполнять вход с личными учетными записями Майкрософт.


