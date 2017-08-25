---
title: "Настройка присоединения Azure AD для ваших пользователей | Документация Майкрософт"
description: "В этой статье объясняется, как администраторы могут настроить присоединение к Azure AD для регистрации локальных каталогов и устройств."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: bfc5d415-c918-4d8b-afee-b3f41cc28469
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 0480c4f89b6036897bc8d7eff81b56262ea8806c
ms.openlocfilehash: e4e0fa77552c4df2ea5bb9ddae916e7c661824d1
ms.contentlocale: ru-ru
ms.lasthandoff: 02/23/2017

---
# <a name="setting-up-azure-ad-join-in-your-organization"></a>Настройка присоединения к Azure AD в организации
Прежде чем приступить к настройке присоединения к Azure Active Directory, необходимо синхронизировать локальный каталог пользователей с облаком или вручную создать управляемые учетные записи в Azure AD.

Подробные инструкции по синхронизации локальных пользователей с Azure AD представлены в разделе [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

Сведения о том, как вручную создать пользователей и управлять ими в Azure AD, содержатся в статье [Управление пользователями в Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Настройка регистрации устройств
1. Войдите на портал Azure с учетной записью администратора.
2. На панели слева выберите **Active Directory**.
3. На вкладке **Каталог** выберите требуемый каталог.
4. Перейдите на вкладку **Настройка** .
5. Откройте раздел **Устройства** .
6. На вкладке **устройства** задайте указанные ниже настройки.  
   * **Максимальное количество устройств на пользователя**: выберите максимальное количество устройств, которое пользователь может иметь в Azure AD.  По достижении этой квоты пользователь больше не сможет добавлять дополнительные устройства до тех пор, пока не будут удалены одно или несколько существующих устройств.
   * **Чтобы присоединить устройства, требуется Multi-factor Auth**: укажите, следует ли пользователям предоставлять второй фактор проверки подлинности для присоединения устройства к Azure AD. Дополнительные сведения о многофакторной идентификации в Azure см. в статье [Приступая к работе с Многофакторной идентификацией Azure в облаке](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).
   * **Пользователи, которые могут присоединять устройства к Azure AD**: выберите пользователей и группы, которым разрешено присоединять устройства к Azure AD.
   * **Дополнительные администраторы присоединенных к Azure AD устройств**: в Azure AD Premium или Enterprise Mobility Suite (EMS) можно выбрать, каким пользователям предоставляются права локального администратора на устройстве. По умолчанию права локального администратора предоставляются глобальным администраторам и владельцам устройств.

<center>![Настройка регистрации устройств](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

После настройки подключения к Azure AD для пользователей они могут подключаться к Azure AD, используя свои корпоративные или личные устройства.

Ниже приведены три сценария предоставления пользователям возможности настроить присоединения к Azure AD.

* Присоединение пользователями устройств, принадлежащих компании, напрямую к Azure AD.
* Присоединение пользователями подключенных к домену устройств, принадлежащих компании, к локальной службе Active Directory и расширение устройства до Azure AD.
* Добавление пользователями рабочих или учебных учетных записей Windows на личные устройства.

## <a name="additional-information"></a>Дополнительная информация
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)


