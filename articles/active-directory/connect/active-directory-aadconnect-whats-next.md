---
title: 'Azure AD Connect: дальнейшие действия и управление Azure AD Connect | Документация Майкрософт'
description: Узнайте, как расширить конфигурацию по умолчанию и операционные задачи для Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f8b73e70606adc2b1fa593745b3ac426c679f417
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592091"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Дальнейшие действия и управление Azure AD Connect
Используйте рабочие процедуры, приведенные в этой статье, для настройки Azure Active Directory (Azure AD) Connect в соответствии с потребностями вашей организации.  

## <a name="add-additional-sync-admins"></a>Добавление дополнительных администраторов синхронизации
По умолчанию только пользователь, выполнивший установку, и локальные администраторы могут управлять установленным модулем синхронизации. Чтобы предоставить дополнительным пользователям доступ к модулю синхронизации и возможность управлять им, найдите группу ADSyncAdmins на локальном сервере и добавьте их в эту группу.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Назначение пользователям лицензий Azure AD Premium и Enterprise Mobility Suite
После синхронизации данных о пользователях с облаком необходимо назначить им лицензию, чтобы они могли приступить к работе с облачными приложениями, такими как Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Назначение лицензии Azure AD Premium или Enterprise Mobility Suite

1. Войдите на портал Azure с учетной записью администратора.
2. Выберите **Active Directory**слева.
3. На странице **Active Directory** дважды щелкните каталог с пользователями для настройки.
4. В верхней части страницы каталога выберите **Лицензии**.
5. На странице **Лицензии** выберите **Active Directory Premium** или **Enterprise Mobility Suite**, а затем щелкните **Назначить**.
6. В диалоговом окне выберите пользователей, которым требуется назначить лицензии, и щелкните значок галочки, чтобы сохранить изменения.

## <a name="verify-the-scheduled-synchronization-task"></a>Проверка выполнения запланированной задачи синхронизации
Используйте портал Azure, чтобы проверить состояние синхронизации.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Чтобы проверить выполнение запланированной задачи синхронизации:
1. Войдите на портал Azure с учетной записью администратора.
2. Выберите **Active Directory**слева.
3. На странице **Active Directory** дважды щелкните каталог с пользователями для настройки.
4. В верхней части страницы каталога выберите вкладку **Интеграция каталогов**.
5. В разделе **integration with local active directory** (Интеграция с активным локальным каталогом Active Directory) просмотрите время последней синхронизации.

<center>![Время синхронизации каталога](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Запуск запланированной задачи синхронизации
Чтобы выполнить задачу синхронизации, можно снова запустить мастер Azure AD Connect.  Для этого требуется указать учетные данные Azure AD.  В мастере выберите задачу **Настроить параметры синхронизации** и нажмите кнопку **Далее** для перехода к следующим страницам мастера. Убедитесь, что на последней странице установлен флажок **Запустить синхронизацию сразу после завершения начальной настройки**.

<center>![Запуск синхронизации](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Дополнительные сведения о планировщике синхронизации Azure AD Connect см. в статье [Синхронизация Azure AD Connect: планировщик](active-directory-aadconnectsync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Дополнительные задачи в Azure AD Connect
После установки Azure AD Connect вы можете повторно запускать мастер с начальной страницы Azure AD Connect или используя ярлык на рабочем столе.  При повторном запуске мастера будут доступны дополнительные задачи.  

Список задач и их краткое описание см. в следующей таблице.

![Список дополнительных задач](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Дополнительная задача | ОПИСАНИЕ |
| --- | --- |
| **Просмотреть выбранный сценарий** |Просмотр текущего решения Azure AD Connect.  Включает общие параметры, синхронизированные каталоги и параметры синхронизации. |
| **Настроить параметры синхронизации** |Изменение текущей конфигурации, например добавление в нее дополнительных лесов Active Directory или активация параметров синхронизации, таких как обратная запись для пользователей, групп, устройств или паролей. |
| **Включить промежуточный режим** |Промежуточные сведения, которые сразу не синхронизируются и не экспортируются в Azure AD или локальной каталог Active Directory.  Эта функция позволяет просмотреть результаты синхронизации до ее выполнения. |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об интеграции локальных удостоверений см. в статье [Подключение Active Directory к Azure Active Directory](active-directory-aadconnect.md).
