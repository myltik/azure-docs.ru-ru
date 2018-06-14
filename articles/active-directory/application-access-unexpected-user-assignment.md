---
title: Назначение пользователей приложениям | Документация Майкрософт
description: Общие сведения о назначении пользователей для приложения в клиенте
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: eff24ddcb02f4aa4cac28d722e73222ca2e686e4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34053359"
---
# <a name="how-to-assign-users-to-applications"></a>Назначение пользователей для приложений

В этой статье представлены общие сведения о назначении пользователей для приложения в клиенте.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Как пользователи назначаются для приложений в Azure AD?

Чтобы пользователь смог получить доступ к приложению, сначала его нужно любым образом назначить этому приложению. Это может сделать администратор, бизнес-делегат или, в некоторых случаях, сам пользователь. Ниже описаны способы назначения пользователя для приложения.

1.  Администратор [назначает пользователей](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) непосредственно для приложения.

2.  Администратор [назначает для приложения группу](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal), в которую входит пользователь. Ею может быть одна из следующих:

  * Группа, синхронизированная из локальной среды.

  * Статическая группа безопасности, созданная в облаке.

  * [Динамическая группа безопасности](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), созданная в облаке.

  * Группа Office 365, созданная в облаке.

  * Группа ["Все пользователи"](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups).

3.  Администратор включает [самостоятельный доступ к приложению](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access), чтобы позволить пользователю добавлять приложения, используя возможность **добавления приложения** на [панели доступа к приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **без бизнес-утверждения**.

4.  Администратор включает [самостоятельный доступ к приложению](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access), чтобы позволить пользователю добавлять приложения, используя возможность **добавления приложения** на [панели доступа к приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **с предварительного разрешения выбранных утверждающих лиц**.

5.  Администратор включает [самостоятельное управление группами](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), чтобы позволить пользователям присоединяться к группам, которым назначено приложение, **без бизнес-утверждения**.

6.  Администратор включает [самостоятельное управление группами](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), чтобы позволить пользователям присоединяться к группам, которым назначено приложение, только **с предварительного разрешения выбранных утверждающих лиц**.

7.  Администратор назначает пользователю лицензии напрямую для приложений Майкрософт, например [Microsoft Office 365](http://products.office.com/).

8.  Администратор назначает группе, в которую входит пользователь, лицензию на приложение Майкрософт, например [Microsoft Office 365](http://products.office.com/).

9.  [Администратор дает свое согласие на использование приложения](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) всеми пользователями, а затем пользователь входит в это приложение.

10. Пользователь сам [дает согласие на использование приложения](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) при входе в него.

## <a name="next-steps"></a>Дополнительная информация
[Управление приложениями с помощью Azure Active Directory](manage-apps/what-is-application-management.md)
