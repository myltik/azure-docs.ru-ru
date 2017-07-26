---
title: "Управление администраторами серверов в службах Azure Analysis Services | Документы Майкрософт"
description: "Узнайте, как управлять администраторами серверов служб Analysis Services в Azure."
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
ms.date: 06/22/2016
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 25c8b49401486edfad0a1a60fe2258a586f6adc2
ms.contentlocale: ru-ru
ms.lasthandoff: 06/23/2017


---
# <a name="manage-server-administrators"></a>Управление администраторами серверов
Администраторами сервера должны быть допустимые пользователи или группы в Azure Active Directory (Azure AD) для клиента, в котором размещен сервер. Для управления администраторами сервера можно использовать элемент **Администраторы служб Analysis Services** в колонке управления сервером на портале Azure или свойства сервера в SSMS. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Добавление администраторов на портале Azure
1. В колонке управления сервером щелкните **Администраторы служб Analysis Services**.
2. В колонке **\<имя_сервера> - Администраторы служб Analysis Services** щелкните **Добавить**.
3. В колонке **добавления администраторов сервера** выберите учетные записи пользователей из Azure AD или пригласите внешних пользователей по электронной почте.

    ![Администраторы сервера на портале Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Добавление администраторов сервера с помощью SSMS
1. Щелкните правой кнопкой мыши сервер и выберите пункт **Свойства**.
2. В разделе **Свойства сервера анализа данных** выберите **Безопасность**.
3. Нажмите кнопку **Добавить**, а затем введите адрес электронной почты для пользователя или группы в Azure AD.
   
    ![Добавление администраторов сервера в SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Дальнейшие действия 
[Аутентификация и пользовательские разрешения](analysis-services-manage-users.md)  
[Управление ролями и пользователями базы данных](analysis-services-database-users.md)  
[Контроль доступа на основе ролей](../active-directory/role-based-access-control-what-is.md)  


