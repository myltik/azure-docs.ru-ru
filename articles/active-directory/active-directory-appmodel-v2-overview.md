---
title: "Обзор конечных точек версии 2.0 | Документация Майкрософт"
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
ms.date: 09/27/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e98ec55dbe5ff0411af124b45bd7ef6177aa957e


---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении
Раньше разработчику приложения, поддерживающего как учетные записи Майкрософт, так и Azure Active Directory, необходимо было обеспечивать интеграцию с двумя отдельными системами.  Мы представляем новую версию API проверки подлинности, которая позволяет выполнять вход пользователей с помощью обоих типов учетных записей в системе Azure AD.  Эта объединенная система проверки подлинности называется **конечной точкой версии 2.0**.  С конечной точкой версии 2.0 одна простая интеграция позволяет охватить миллионы пользователей с личными, рабочими или учебными учетными записями.

Приложения, использующие конечную точку версии 2.0, также могут использовать REST API из [Microsoft Graph](https://graph.microsoft.io) и [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) с помощью любого типа учетной записи.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Приступая к работе
[!VIDEO https://channel9.msdn.com/Events/Build/2016/P530/player]


Выберите платформу в следующем списке, чтобы создать приложение с помощью наших библиотек и платформ с открытым исходным кодом.  Вы также можете воспользоваться документацией по протоколу OAuth 2.0 и OpenID Connect для отправки и получения сообщений протокола напрямую, без использования библиотеки аутентификации.

<!-- TODO: Finalize this table  -->
[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Новые возможности
Основные сведения, приведенные здесь, будут полезны в понимании того, что возможно и что нет в конечной версии 2.0.

* Узнайте о [типах приложений, которые можно создавать с помощью конечной точки версии 2.0](active-directory-v2-flows.md).
* Ознакомьтесь с [ограничениями](active-directory-v2-limitations.md) конечной точки версии 2.0.
* Недавно мы добавили поддержку [областей только для администраторов](active-directory-v2-scopes.md) и [предоставления учетных данных клиента OAuth2](active-directory-v2-protocols-oauth-client-creds.md).  Попробуйте сами!

## <a name="reference"></a>Справочные материалы
Следующие ссылки позволят глубже изучить платформу:

* Build 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
* Получите справку по переполнению стека, используя теги [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) или [adal](http://stackoverflow.com/questions/tagged/adal).
* [Справочник по протоколу версии 2.0](active-directory-v2-protocols.md)
* [Справочник по маркерам версии 2.0](active-directory-v2-tokens.md)
* [Справочник по библиотеке версии 2.0](active-directory-v2-libraries.md)
* [Области и разрешения в конечной точке версии 2.0](active-directory-v2-scopes.md)
* [Портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
* [Справочник по API REST Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
* [Microsoft Graph](https://graph.microsoft.io)




<!--HONumber=Dec16_HO4-->


