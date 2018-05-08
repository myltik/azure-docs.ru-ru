---
title: Добавление или удаление пользователей в Azure Active Directory | Документация Майкрософт
description: Инструкции по добавлению новых или удалению существующих пользователей в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 3e8b7061e57c1baf222e5f565a5b7efc6b997507
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Краткое руководство по добавлению новых пользователей в Azure Active Directory
Из этой статьи вы узнаете, как удалять или добавлять пользователей в клиенте Azure Active Directory (Azure AD) в вашей организации с помощью портала Azure или синхронизации данных учетной записи пользователя Windows Server AD в локальной среде. 

## <a name="add-cloud-based-users"></a>Добавление облачных пользователей
1. Войдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Azure Active Directory**, а затем щелкните **Пользователи и группы**.
3. В колонке **Пользователи и группы** выберите **Все пользователи** и щелкните **Новый пользователь**.
   ![Выбор команды "Добавить"](./media/add-users-azure-active-directory/add-user.png)
4. Введите сведения о пользователе в соответствующих полях, например **Имя** и **Имя пользователя**. Частью имени пользователя, представляющей собой доменное имя, должно быть доменное имя по умолчанию, [domain name].onmicrosoft.com, или проверенное доменное имя, не являющееся федеративным, например [настроить доменное имя](add-custom-domain.md) contoso.com.
5. Скопируйте или иным способом запишите созданный пароль пользователя, чтобы после завершения этого процесса его можно было предоставить пользователю.
6. При необходимости можно указать сведения, открыв колонку **Профиль**, **Группы** или **Роль каталога** для пользователя. Дополнительные сведения о ролях пользователей и администраторов см. в статье [Назначение ролей администратора в Azure AD](active-directory-assign-admin-roles-azure-portal.md).
7. В разделе **Пользователь** выберите **Создать**.
8. Безопасным способом передайте созданный пароль новому пользователю, чтобы он мог войти в систему.

> [!TIP]
> Вы также можете синхронизировать данные учетной записи пользователя из локальной среды Windows Server AD. Решения для идентификации Майкрософт обладают локальными и облачными возможностями, создавая единое удостоверение пользователя для проверки подлинности и авторизации для всех ресурсов, независимо от их расположения. Мы называем это гибридной идентификацией. [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) можно использовать для интеграции локальных каталогов с Azure Active Directory для сценариев гибридной идентификации. Таким образом вы сможете предоставить пользователям возможность получать доступ с использованием одного удостоверения для приложений Office 365, Azure и программного обеспечения как услуги (SaaS), интегрированных с Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Удалить пользователей из Azure AD
1. Войдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Пользователи и группы**.
3. В колонке **Пользователи и группы** выберите пользователя, которого необходимо удалить из списка. 
4. В колонке для выбранного пользователя щелкните **Обзор**, а затем на панели команд щелкните **Удалить**.
   ![Выбор команды "Добавить"](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Подробнее 
* [Добавление гостевых пользователей из другого каталога](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Управление профилями пользователей](active-directory-users-profile-azure-portal.md)
* [Восстановление удаленного пользователя](active-directory-users-restore.md)



## <a name="next-steps"></a>Дополнительная информация
В этом кратком руководстве описано, как добавить личный домен в Azure AD Premium. 

Чтобы создать личный домен в Azure AD с портала Azure, воспользуйтесь следующей ссылкой.

> [!div class="nextstepaction"]
> [Добавление пользователей в Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 