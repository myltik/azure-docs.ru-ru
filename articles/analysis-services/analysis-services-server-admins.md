---
title: "Управление администраторами серверов в службах Azure Analysis Services | Документы Майкрософт"
description: "Узнайте, как управлять администраторами серверов служб Analysis Services в Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 6a42baf9fd880264e4130c009f27935a4743de21
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2017
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

