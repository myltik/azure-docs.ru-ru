---

title: "Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory? | Документация Майкрософт"
description: "Служба совместной работы Azure Active Directory B2B позволяет информационным работникам добавлять в Azure AD пользователей своей организации для доступа к корпоративным приложениям."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/02/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d9ffd4176e87b6b5ada882ff09f507665bda7b1d
ms.openlocfilehash: 7bad8269c3756979fe48d130a0cdfb02ceafad86


---

# <a name="how-do-information-workers-add-b2b-collaboration-users-to-azure-active-directory"></a>Как информационные работники могут добавить пользователей службы совместной работы B2B в Azure Active Directory?

Информационные работники могут использовать [панель доступа приложения](http://myapps.microsoft.com), чтобы добавлять пользователей службы совместной работы B2B в группы и приложения, которые они администрируют.

## <a name="information-workers-adding-b2b-collaboration-users-to-an-application"></a>Как информационные работники могут добавить пользователей службы совместной работы B2B в приложение?
Как информационный работник в партнерской организации назначьте приложению пользователей службы совместной работы B2B. Это процесс показан в следующем видеоролике:

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps]

  Если этот видеоролик не отображается, то вы можете его найти [здесь](https://channel9.msdn.com/Blogs/Azure/information-worker-assign-to-apps).

## <a name="information-workers-adding-b2b-collaboration-users-to-a-group"></a>Как информационные работники могут добавить пользователей службы совместной работы B2B в группу?

Информационные работники могут просто добавить пользователей службы совместной работы B2B в любую назначенную группу, для которой включено самостоятельное управление группами.
> [!NOTE]
Вы можете добавить пользователей службы совместной работы B2B в динамическую группу или группу, синхронизированную с локальной службой Active Directory.

## <a name="add-without-invitation"></a>Добавление без приглашения

Если приглашающий пользователь принадлежит к роли, обладающей привилегиями перечисления в каталоге партнерской организации, из которого он добавляет пользователей, то приглашенные пользователи добавляются в приглашающую организацию без отправки приглашения.

Ниже описан сценарий, в котором это может пригодиться:
1. Пользователь в главной организации (например, WoodGrove) приглашает одного пользователя из партнерской организации (например, Sam@litware.com) в качестве гостя.
2. Администратор главной организации настраивает политики, позволяющие Сэму (Sam) идентифицировать и добавлять других пользователей из партнерской организации (Litware).
4. Теперь Сэм может добавлять других пользователей из Litware в каталог, группы или приложения WoodGrove без отправки и активации приглашений. Если у Сэма есть соответствующие привилегии на перечисление в Litware, то это происходит автоматически.


* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Как администраторы Azure Active Directory могут добавить пользователей службы совместной работы B2B?](active-directory-b2b-admin-add-users.md)
* [Элементы сообщения с приглашением в службу совместной работы B2B](active-directory-b2b-invitation-email.md)
* [Активация приглашения службы совместной работы B2B](active-directory-b2b-redemption-experience.md)
* [Руководство по лицензированию службы совместной работы Azure Active Directory B2B](active-directory-b2b-licensing.md)
* [Устранение неполадок службы совместной работы Azure Active Directory B2B](active-directory-b2b-troubleshooting.md)
* [Часто задаваемые вопросы о службе совместной работы Azure Active Directory B2B](active-directory-b2b-faq.md)
* [API службы совместной работы Azure Active Directory B2B и настройка](active-directory-b2b-api.md)
* [Многофакторная идентификация для пользователей службы совместной работы B2B](active-directory-b2b-mfa-instructions.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO1-->


