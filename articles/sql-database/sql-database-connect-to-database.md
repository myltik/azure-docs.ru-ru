<properties
	pageTitle="Подключение к базе данных SQL Azure с помощью среды SSMS" metaKeywords=""
	description="Узнайте, как подключиться к базе данных SQL Azure с помощью среды SSMS."
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" 
	manager="jhubbard" 
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/25/2015"
	ms.author="sidneyh" />

# Подключение к Базе данных SQL Azure с помощью SQL Server Management Studio

В этой статье приведены действия, необходимые для подключения к Базе данных SQL Microsoft Azure с помощью среды SQL Server Management Studio (SSMS).

## Предварительные требования
* Подготовленная и запущенная База данных SQL Azure. Информацию о создании базы данных читайте в статье [Начало работы с Базой данных SQL Microsoft Azure](sql-database-get-started.md).
* Имя и пароль администратора Базы данных SQL.
* Среда SQL Server Management Studio 2014. Чтобы получить ее, [загрузите SQL Express](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx).
* Настройте параметры брандмауэра для базы данных. См. [Практическое руководство. Настройка параметров брандмауэра (база данных Azure SQL)](sql-database-configure-firewall-settings.md)

## Для подключения к экземпляру Базы данных SQL
1. Войдите на [портал управления Azure](https://portal.azure.com).
2. Нажмите кнопку **Обзор**, а затем выберите **Базы данных SQL** (b).

	![Нажмите кнопку «Обзор» и выберите «База данных SQL»][1]
3. Выделив группу **Базы данных SQL** (a), щелкните имя базы данных на сервере, к которому хотите подключиться (b).

	![Щелкните имя базы данных][2]
4. Выделив имя (a), нажмите кнопку «Свойства» (b). Скопируйте имя сервера (c) и имя администратора (d). Имя и пароль администратора создаются вместе с Базой данных SQL. Для перехода к следующему шагу вам потребуется пароль.

	![Щелкните «SQL Server», «Параметры» и «Свойства»][3]
5. Откройте среду SQL Server Management Studio 2014.
6. В диалоговом окне «Соединение с сервером» вставьте скопированное имя сервера в поле **Имя сервера** (a). В раскрывающемся списке «Аутентификация» выберите значение **Аутентификация SQL Server** (b). Вставьте имя администратора сервера в поле **Имя для входа** (c) и введите пароль (d). Щелкните **Параметры** (e).

	![Диалоговое окно входа в SSMS][4]
7. На вкладке «Свойства подключения» выберите в списке **Подключение к базе данных** значение **master** (или укажите другую базу данных, к которой вы хотите подключиться). Затем нажмите кнопку **Подключить**.

	![Выберите значение «master» и щелкните «Соединить»][5]

## Устранение неполадок с подключением

В случае возникновения проблем ознакомьтесь со статьей [Устранение неполадок при подключении к Базе данных SQL Azure](https://support.microsoft.com/kb/2980233/) Список возможных проблем и способов их устранения см. на странице [Устранение неполадок подключения к Базе данных SQL Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).


## Дальнейшие действия
Для создания баз данных и управления ими можно использовать инструкции Transact-SQL. Дополнительные сведения см. в статьях [СОЗДАНИЕ БАЗЫ ДАННЫХ (База данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) и [Управление Базой данных SQL Azure с помощью SQL Server Management Studio](sql-database-manage-azure-ssms.md). Также вы можете регистрировать события в хранилище Azure. Дополнительные сведения см. в статье [Начало работы с аудитом базы данных SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=July15_HO2-->