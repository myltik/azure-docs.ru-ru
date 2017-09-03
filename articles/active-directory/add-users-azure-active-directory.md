---
title: "Добавление новых пользователей в Azure Active Directory | Документация Майкрософт"
description: "Инструкции по добавлению новых пользователей в Azure Active Directory."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 13a7d2d3b991206c45e66872b590bc27a224eead
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Краткое руководство по добавлению новых пользователей в Azure Active Directory
В этой статье объясняется, как добавлять новых пользователей в вашей организации в Azure Active Directory (Azure AD) одновременно с помощью портала Azure или синхронизации данных учетной записи пользователя Windows Server AD в локальной среде. 

## <a name="add-cloud-based-users"></a>Добавление облачных пользователей
1. Войдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Azure Active Directory**, а затем щелкните **Пользователи и группы**.
3. В колонке **Пользователи и группы** выберите **Все пользователи** и щелкните **Новый пользователь**.
   ![Выбор команды "Добавить"](./media/add-users-azure-active-directory/add-user.png)
4. Введите сведения о пользователе в соответствующих полях, например **Имя** и **Имя пользователя**. Частью имени пользователя, представляющей собой доменное имя, должно быть доменное имя по умолчанию, [domain name].onmicrosoft.com, или проверенное доменное имя, не являющееся федеративным, например [настроить доменное имя](add-custom-domain.md) contoso.com.
5. Скопируйте или иным способом запишите созданный пароль пользователя, чтобы после завершения этого процесса его можно было предоставить пользователю.
6. При необходимости можно заполнить сведения, открыв колонку **Профиль**, **Группы** или **Роль каталога** для пользователя. Дополнительные сведения о ролях пользователей и администраторов см. в статье [Назначение ролей администратора в Azure Active Directory](active-directory-assign-admin-roles.md).
7. В колонке **Пользователь** щелкните **Создать**.
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
* [Добавление внешнего пользователя](active-directory-users-create-external-azure-portal.md)

* [Назначение пользователей в предварительной версии Azure AD](active-directory-users-assign-role-azure-portal.md)

## <a name="next-steps"></a>Дальнейшие действия
В этом кратком руководстве описано, как добавить личный домен в Azure AD Premium. 

Чтобы создать личный домен в Azure AD с портала Azure, воспользуйтесь следующей ссылкой.

> [!div class="nextstepaction"]
> [Добавление пользователей в Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 
