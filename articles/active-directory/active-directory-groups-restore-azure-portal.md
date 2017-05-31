---

title: "Восстановление удаленной группы Office 365 в Azure Active Directory | Документы Майкрософт"
description: "Описывает, как в Azure Active Directory восстановить удаленную группу, просмотреть доступные для восстановления группы или удалить группу без возможности восстановления"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 473f3e464324cc2667428485235e3b942c736382
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Восстановление удаленной группы Office 365 в Azure Active Directory

Когда вы удаляете группу Office 365 в Azure Active Directory (Azure AD), эта группа в течение 30 дней с момента удаления сохраняется, но не отображается. Это сделано для того, чтобы группу и ее содержимое можно было при необходимости восстановить. Эта функция действует в Azure AD исключительно для групп Office 365. Она недоступна для групп безопасности или групп рассылки.

Для восстановления группы нужны разрешения любого из следующих видов.

Роль  | Разрешения 
--------- | ---------
Администратор компании, партнер с поддержкой 2 уровня; администратор службы InTune | Восстановление любой удаленной группы Office 365 
Администратор учетных записей, партнер с поддержкой 1 уровня | Восстановление любой удаленной группы Office 365, кроме тех, которым назначена роль администратора компании 
Пользователь | Восстановление любой удаленной группы Office 365, которая принадлежала им 


## <a name="how-to-view-deleted-office-365-groups-that-are-available-to-restore"></a>Просмотр удаленных групп Office 365, которые доступны для восстановления
Следующие командлеты можно использовать для просмотра удаленных групп, чтобы убедиться, что нужные группы еще не удалены окончательно. Эти командлеты являются частью [модуля Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Дополнительные сведения об этом модуле можно найти в статье [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0) (Модуль PowerShell версии 2 для Azure Active Directory).

1.    Запустите следующий командлет, чтобы отобразить в клиенте все удаленные группы Office 365, которые доступны для восстановления.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.    Если вам известен идентификатор объекта для конкретной группы (его можно получить из командлета, описанного в шаге 1), вы можете с выполнить следующий командлет, чтобы убедиться, что эта группа еще не удалена без возможности восстановления.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-an-office-365-group"></a>Как восстановить группу Office 365
Когда вы убедитесь, что группа еще доступна для восстановления, вы можете восстановить эту удаленную группу, выполнив одно из следующих действий. Если группа содержит документы, узлы SP или другие постоянные объекты, полное восстановление группы и ее содержимого может занять до 24 часов.

1.    Запустите следующий командлет, чтобы восстановить группу и ее содержимое.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

Кроме того, следующий командлет позволяет окончательно удалить группу без возможности восстановления.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Как убедиться, что действие выполнено?
Чтобы убедиться, что группа Office 365 успешно восстановлена, запустите командлет `Get-AzureADGroup –ObjectId <objectId>` для отображения сведений о группе. После выполнения запроса на восстановление происходят следующие изменения.
- Группа появится в навигационной панели в левой части Exchange.
- В планировщике отобразится план для этой группы.
- Станут доступны все сайты Sharepoint и их содержимое.
- Группа станет доступна из любой конечной точки Exchange и из других рабочих нагрузок Office 365, которые поддерживают группы Office 365.


## <a name="next-steps"></a>Дальнейшие действия
В следующих статьях содержатся дополнительные сведения о группах Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление участниками группы](active-directory-groups-members-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)

