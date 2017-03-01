---
title: "Управление пользователями в службах Azure Analysis Services | Документация Майкрософт"
description: "Узнайте, как управлять пользователями на сервере служб Analysis Services в Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>Управление пользователями в службах Azure Analysis Services
В службах Azure Analysis Services существует два типа пользователей, администраторы сервера и пользователи базы данных. 

## <a name="server-administrators"></a>Администраторы сервера
Для управления администраторами сервера можно использовать элемент **Администраторы служб Analysis Services** в колонке управления сервером на портале Azure или свойства сервера в SSMS. Администраторы служб Analysis Services — это администраторы сервера базы данных с правами на выполнение общих задач администрирования базы данных, таких как добавление и удаление баз данных, а также управление пользователями. По умолчанию пользователь, который создает сервер на портале Azure, автоматически добавляется в список администраторов служб Analysis Services.

![Администраторы сервера на портале Azure](./media/analysis-services-manage-users/aas-manage-users-admins.png)

Полезная информация:

* службы Azure Analysis Services не поддерживают тип удостоверения Windows Live ID;  
* администраторы служб Analysis Services должны быть действительными пользователями Azure Active Directory;
* при создании сервера служб Azure Analysis Services с помощью шаблонов Azure Resource Manager администраторы служб Analysis Services добавляются из массива JSON пользователей.

В качестве администраторов служб Analysis Services и администраторов ресурсов Azure (управляют ресурсами для подписки Azure) могут использоваться разные пользователи. Это позволяет поддерживать совместимость с управляющим поведением XMLA и TMSL в службах Analysis Services, а также распределять обязанности управления ресурсами Azure и базой данных Analysis Services. Чтобы просмотреть все роли и типы доступа для своих ресурсов служб Azure Analysis Services, используйте параметр "Управление доступом (IAM)" в колонке "Управление".

### <a name="to-add-administrators-using-azure-portal"></a>Добавление администраторов с помощью портала Azure
1. В колонке управления сервером щелкните **Администраторы служб Analysis Services**.
2. В колонке **\<имя_сервера> - Администраторы служб Analysis Services** щелкните **Добавить**.
3. В колонке **Добавление администраторов сервера** выберите учетные записи пользователей для добавления.

## <a name="database-users"></a>Пользователи базы данных
Пользователи базы данных должны быть добавлены в роли базы данных. Роли определяют пользователей и группы, которые имеют одинаковые разрешения для базы данных. По умолчанию у баз данных с табличной моделью есть стандартная роль Users с разрешениями на чтение. Дополнительные сведения см. в разделе [Роли (табличные службы SSAS)](https://msdn.microsoft.com/library/hh213165.aspx).

Пользователи в модели базы данных Azure Analysis Services *должны быть пользователями вашей службы Azure Active Directory*. В качестве имен пользователей необходимо использовать адрес электронной почты организации или имя участника-пользователя. Это отличается от локальных баз данных с табличной моделью, которые поддерживают имена пользователей домена Windows. 

Вы можете создавать роли базы данных, добавлять пользователей и группы в роли и настраивать безопасность на уровне строк в SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS). Можно также добавлять или удалять пользователей в ролях с помощью [командлетов PowerShell для служб Analysis Services](https://msdn.microsoft.com/library/hh758425.aspx) или [языка TMSL](https://msdn.microsoft.com/library/mt614797.aspx).

**Пример скрипта TMSL**

В этом примере пользователь и группа добавляются в роль Users для базы данных SalesBI.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>Управление доступа на основе ролей

Администраторы подписок могут использовать **управление доступом** в колонке управления для настройки ролей. Это отличается от настройки администраторов сервера или пользователей базы данных, которые, как описано выше, настраиваются на уровне сервера или базы данных. 

![Управление доступом на портале Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Роли применяются к пользователям или учетным записям, которым необходимо выполнить задачи на портале или с помощью шаблонов Azure Resource Manager. Дополнительные сведения см. в статье об [управлении доступом на основе ролей](../active-directory/role-based-access-control-what-is.md).

## <a name="next-steps"></a>Дальнейшие действия
Если вы еще не развернули табличную модель на сервере, сейчас самое время это сделать. Дополнительные сведения см. в статье [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Развертывание в службы Azure Analysis Services).

Если модель для сервера развернута, к ней можно подключиться с помощью клиента или браузера. Дополнительные сведения см. в статье [Получение данных из служб Azure Analysis Services](analysis-services-connect.md).


