---
title: "Дополнительные возможности управления доступом с помощью групп | Документация Майкрософт"
description: "Расширенные указания по управлению группами безопасности и использованию этих групп для управления доступом к ресурсу."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 82fbeb379e90add09f7c569111053f6e9b1bc9c5
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---
# <a name="managing-owners-for-a-group"></a>Управление владельцами группы
После того как владелец ресурса предоставил доступ к ресурсу группе Azure Active Directory, управление членством в группе осуществляется владельцем группы. Владелец ресурса фактически делегирует владельцу группы разрешение предоставлять пользователям доступ к ресурсу.

> [!IMPORTANT]
> Для управления службой Azure AD мы рекомендуем использовать [Центр администрирования Azure AD](https://aad.portal.azure.com) на портале Azure, а не классический портал Azure, который упоминается в этой статье. 

## <a name="assigning-group-ownership"></a>Назначение владельца группы
**Добавление владельца в группу**

1. На [классическом портале Azure](https://manage.windowsazure.com)щелкните **Active Directory**, а затем откройте каталог своей организации.
2. Откройте вкладку **Группы** , а затем группу, в которую нужно добавить владельцев.
3. Щелкните **Добавить владельцев**.
4. На странице **Добавление владельцев** выберите пользователя, которого нужно добавить в качестве владельца этой группы, и убедитесь в том, что его имя появилось в области **Выбранные**.

**Удаление владельца из группы**

1. На [классическом портале Azure](https://manage.windowsazure.com)щелкните **Active Directory**, а затем откройте каталог своей организации.
2. Откройте вкладку **Группы** , а затем группу, из которой нужно удалить владельца.
3. Откройте вкладку **Владельцы** .
4. Выберите владельца, которого нужно удалить из группы, а затем щелкните **Удалить**.

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Azure Active Directory cmdlets for configuring group settings](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Что такое Microsoft Azure Active Directory](active-directory-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

