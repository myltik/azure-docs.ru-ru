<properties
	pageTitle="Копирование данных из хранилища BLOB-объектов в базу данных SQL | Microsoft Azure"
	description="В этом учебнике рассказывается, как использовать действие копирования в конвейере фабрики данных Azure для копирования данных из хранилища BLOB-объектов в базу данных SQL."
	Keywords="BLOB-объект, SQL, хранилище BLOB-объектов, копирование данных"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных 
> [AZURE.SELECTOR]
- [Обзор и предварительные требования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Портал Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [ИНТЕРФЕЙС REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)

В этом учебнике вы создадите фабрику данных с конвейером, чтобы скопировать данные из хранилища BLOB-объектов в базу данных SQL.

Действие копирования перемещает данные в фабрике данных Azure. Это действие выполняется с помощью глобально доступной службы, обеспечивающей безопасное, надежное и масштабируемое копирование данных между разными хранилищами. Дополнительные сведения о действии копирования см. в статье [Действия перемещения данных](data-factory-data-movement-activities.md).

> [AZURE.NOTE] Подробный обзор службы фабрики данных см. в статье [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке][data-factory-introduction].

##Предварительные требования для прохождения этого учебника
Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure**. Если у вас нет подписки, вы можете создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. в статье [Бесплатная пробная версия][azure-free-trial].
- **Учетная запись хранения Azure** В этом учебнике будет использоваться хранилище больших двоичных объектов в качестве **исходного** хранилища данных. Если у вас нет учетной записи хранения Azure, инструкции по ее созданию см. в статье [Об учетных записях хранения Azure][data-factory-create-storage].
- **База данных SQL Azure**. В этом учебнике используется база данных SQL Azure в качестве **конечного** хранилища данных. Если нет базы данных SQL Azure, которую можно использовать для изучения этого учебника, см. раздел [Как создать и настроить базу данных SQL Azure][data-factory-create-sql-database], чтобы создать такую базу данных.
- **SQL Server 2012/2014 или Visual Studio 2013**. Для создания образца базы данных и просмотра итоговых данных в базе данных используется SQL Server Management Studio или Visual Studio .

## Получение имени и ключа учетной записи хранения BLOB-объектов 
В ходе изучения этого учебника потребуются имя и ключ вашей учетной записи хранения Azure. Запишите **имя** и **ключ учетной записи** для своей учетной записи хранения Azure.

1. Войдите на [портал Azure][azure-portal].
2. Щелкните раздел **ОБЗОР** слева и выберите **Учетные записи хранения**.
3. В колонке **Учетные записи хранения** выберите **учетную запись хранения Azure**, которая будет использоваться в ходе изучения этого учебника.
4. В разделе **Параметры** выберите ссылку **Ключи доступа**.
5.  Нажмите кнопку **Копировать** (изображение) рядом с текстовым полем **Имя учетной записи хранения**, вставьте скопированный текст, например, в текстовый файл и сохраните его.
6. Повторите предыдущий шаг для ключа **key1**.
7. Закройте все колонки, нажав **X**.

## Получение имени сервера SQL, имени базы данных и имени пользователя
В ходе изучения этого учебника потребуются имена сервера Azure SQL Server, базы данных и пользователя. Запишите имена **сервера**, **базы данных** и **пользователя** базы данных SQL Azure.

1. На **портале Azure** щелкните **ОБЗОР** слева и выберите **Базы данных SQL**.
2. В **колонке баз данных SQL** выберите **базу данных**, которую вы планируете использовать для этого учебника. Запишите **имя базы данных**.
3. В колонке **БАЗА ДАННЫХ SQL** щелкните плитку **СВОЙСТВА**.
4. Запишите значения параметров **ИМЯ СЕРВЕРА** и **ИМЯ ВХОДА АДМИНИСТРАТОРА СЕРВЕРА**.
5. Закройте все колонки, нажав **X**.

## Предоставление службам Azure доступа к серверу SQL 
Убедитесь, что настройка **Разрешить доступ к службам Azure** имеет состояние **ВКЛ** для вашего сервера Azure SQL Server, чтобы служба фабрики данных могла иметь доступ к серверу Azure SQL Server. Чтобы проверить и при необходимости включить этот параметр, сделайте следующее.

1. Щелкните раздел **ОБЗОР** слева и выберите пункт **Серверы SQL**.
2. Выберите **свой сервер** и нажмите **ПАРАМЕТРЫ** в колонке **SQL SERVER**.
3. В колонке **ПАРАМЕТРЫ** щелкните пункт **Брандмауэр**.
4. В колонке **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.
5. Закройте все колонки, нажав **X**.

## Подготовка хранилища BLOB-объектов и базы данных SQL 
Теперь подготовьте хранилище больших двоичных объектов Azure и базу данных SQL Azure к изучению этого учебника, выполнив следующие действия.

1. Запустите блокнот, вставьте следующий текст и сохраните файл на жестком диске под именем **emp.txt** в папке **C:\\ADFGetStarted**.

        John, Doe
		Jane, Doe

2. При помощи таких средств, как [обозреватель хранилища Azure](https://azurestorageexplorer.codeplex.com/), создайте контейнер **adftutorial** и загрузите файл **emp.txt** в этот контейнер.

    ![Обозреватель хранилищ Azure Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Используйте следующий скрипт SQL, чтобы создать таблицу **emp** в базе данных SQL Azure.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Если на вашем компьютере установлен SQL Server 2012/2014**, следуйте инструкциям в статье [Шаг 2. Подключение к базе данных SQL управляющей базы данных SQL Azure при помощи SQL Server Management Studio][sql-management-studio] для подключения к серверу Azure SQL Server и запуска скрипта SQL. В этой статье для настройки брандмауэра сервера Azure SQL Server используется [классический портал Azure](http://manage.windowsazure.com), а не [новый портал Azure](https://portal.azure.com).

	Если клиенту не разрешен доступ к серверу Azure SQL Server, то следует настроить брандмауэр вашего сервера Azure SQL Server, чтобы разрешить доступ с вашей машины (IP-адрес). В [этой статье](../sql-database/sql-database-configure-firewall-settings.md) описано, как настроить брандмауэр для сервера Azure SQL Server.

Необходимые условия выполнены. Щелкните вкладку в верхней части, чтобы использовать для работы с руководством следующие средства:

- Портал Azure
- Visual Studio
- PowerShell
- Интерфейс REST API
- .NET API
- Мастер копирования

<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0921_2016-->