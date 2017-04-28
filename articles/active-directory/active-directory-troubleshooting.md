---
title: 'Troubleshooting: ''Active Directory'' item is missing or not available | Microsoft Docs'
description: "Что делать, если пункт меню Active Directory не отображается на портале управления Azure."
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9c52e9257272c5b0c55c488d2c7ca6fef500260b
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Устранение неполадок: элемент Active Directory отсутствует или недоступен
Многие указания по использованию функций и служб Azure Active Directory начинаются с «Перейдите на портал управления Azure и щелкните **Active Directory**». Но что делать, если расширение или пункт меню Active Directory не отображается или помечен **Недоступно**? В этой статье вы найдете нужные ответы. В ней описываются условия, при которых элемент **Active Directory** не отображается или недоступен, и дальнейшие действия в такой ситуации.

## <a name="active-directory-is-missing"></a>Active Directory отсутствует
Как правило, элемент **Active Directory** отображается в левом меню навигации. В указаниях процедур Azure Active Directory предполагается, что этот элемент отображается в представлении.

![Снимок экрана: Active Directory в Azure](./media/active-directory-troubleshooting/typical-view.png)

Элемент Active Directory отображается в левом меню навигации при выполнении любого из следующих условий. В противном случае он не отображается.

* Текущий пользователь вошел в систему через учетную запись Майкрософт (ранее известную как Windows Live ID).
  
    ИЛИ
* У клиента Azure есть каталог, а владелец текущей учетной записи является администратором каталога.
  
    ИЛИ
* У клиента Azure есть хотя бы одно пространство имен контроля доступа Azure AD (ACS). Дополнительные сведения см. в статье о [пространстве имен контроля доступа](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    ИЛИ
* У клиента Azure есть хотя бы один поставщик Azure Multi-Factor Authentication. Дополнительные сведения см. в статье [Администрирование поставщиков Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Чтобы создать пространство имен контроля доступа или поставщик Multi-Factor Authentication, щелкните **+Создать** > **Службы приложений** > **Active Directory**.

Чтобы получить права администратора для каталога, обратитесь к администратору, чтобы он назначил вашей учетной записи роль администратора. Дополнительные сведения см. в статье [Назначение ролей администратора в Azure Active Directory](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory недоступен
Элемент **Active Directory** отобразится, если щелкнуть **+Создать** > **Службы приложений**. В частности, элемент Active Directory появляется, когда любая из функций Active Directory, такая как каталог, управление доступом или поставщик Multi-Factor Auth, доступна текущему пользователю.

Однако при загрузке страницы элемент будет недоступен и помечен **Недоступно**. Это временное состояние. Если подождать несколько секунд, элемент станет доступным. Если задержка длится дольше, зачастую достаточно просто обновить веб-страницу.

![Снимок экрана: Active Directory недоступен](./media/active-directory-troubleshooting/not-available.png)


