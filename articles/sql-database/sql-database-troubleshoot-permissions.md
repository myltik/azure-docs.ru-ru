<properties
	pageTitle="Как выполнять задачи администрирования, включая сброс пароля администратора | Microsoft Azure"
	description="Выполнение распространенных административных задач в базе данных SQL, включая сброс пароля администратора, а также предоставление и отзыв доступа."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="сброс пароля администратора"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-shysun"/>

# Как выполнять распространенные административные задачи, включая сброс пароля администратора, в базе данных SQL Azure
В этом разделе вы найдете инструкции по быстрому предоставлению и отмене доступа к базе данных SQL Azure. Более подробную информацию см. в следующих статьях:

- [Управление базами данных и именами входа в Базе данных SQL Azure](sql-database-manage-logins.md)
- [Защита базы данных SQL](sql-database-security.md)
- [Центр обеспечения безопасности для ядра СУБД SQL Server и базы данных Azure SQL](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Сброс пароля администратора для логического сервера

- На [портале Azure](https://portal.azure.com) щелкните **Серверы SQL**, выберите в списке сервер, и нажмите кнопку **Сбросить пароль**.

## Предоставление доступа к серверу только авторизованным IP-адресам
- См. [Практическое руководство. Настройка параметров брандмауэра для базы данных SQL](sql-database-configure-firewall-settings.md).

## Создание пользователей автономной базы данных в пользовательской базе данных
- Используйте инструкцию [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx). Ознакомьтесь со статьей [Пользователи автономной базы данных — создание переносимой базы данных](https://msdn.microsoft.com/library/ff929188.aspx).

## Проверка подлинности пользователей автономной базы данных с помощью Azure Active Directory
- Ознакомьтесь со статьей [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).

## Создание дополнительных имен входа для привилегированных пользователей в виртуальной базе данных master
- Используйте оператор [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx). Дополнительные сведения см. в разделе с описанием управления именами для входа в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).

<!---HONumber=AcomDC_0713_2016-->