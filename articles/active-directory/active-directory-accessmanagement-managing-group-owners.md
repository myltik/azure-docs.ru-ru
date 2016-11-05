
---
title: Дополнительные возможности управления доступом с помощью групп | Microsoft Docs
description: Расширенные указания по управлению группами безопасности и использованию этих групп для управления доступом к ресурсу.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: curtand

---
# <a name="managing-owners-for-a-group"></a>Управление владельцами группы
После того как владелец ресурса предоставил доступ к ресурсу группе Azure Active Directory, управление членством в группе осуществляется владельцем группы. Владелец ресурса фактически делегирует владельцу группы разрешение предоставлять пользователям доступ к ресурсу.

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

<!--HONumber=Oct16_HO2-->


