---
title: "Как выполнять задачи администрирования, включая сброс пароля администратора | Документация Майкрософт"
description: "Выполнение распространенных административных задач в базе данных SQL, включая сброс пароля администратора, а также предоставление и отзыв доступа."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "сброс пароля администратора"
ms.assetid: 9803fdcf-6501-4ac4-8cd0-f80071e052e1
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1a175572bf541f0be3936ad8934f52ea6b90dfa1


---
# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Как выполнять распространенные административные задачи, включая сброс пароля администратора, в базе данных SQL Azure
В этой статье вы найдете инструкции по быстрому предоставлению и отмене доступа к базе данных SQL Azure. Более подробную информацию см. в следующих статьях:

* [Управление базами данных и именами входа в Базе данных SQL Azure](sql-database-manage-logins.md)
* [Защита базы данных SQL](sql-database-security.md)
* [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Сброс пароля администратора для логического сервера
* На [портале Azure](https://portal.azure.com) щелкните **Серверы SQL**, выберите в списке сервер и щелкните **Сбросить пароль**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Предоставление доступа к серверу только авторизованным IP-адресам
* См. статью [Настройка правила брандмауэра уровня сервера базы данных SQL Azure с помощью портала Azure](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Создание пользователей автономной базы данных в пользовательской базе данных
* Используйте инструкцию [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx). Дополнительные сведения см. в статье [Пользователи автономной базы данных — создание переносимой базы данных](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Проверка подлинности пользователей автономной базы данных с помощью Azure Active Directory
* Ознакомьтесь со статьей [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Создание дополнительных имен входа для привилегированных пользователей в виртуальной базе данных master
* Используйте оператор [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx). Дополнительные сведения см. в разделе с описанием управления именами для входа в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).




<!--HONumber=Nov16_HO3-->


