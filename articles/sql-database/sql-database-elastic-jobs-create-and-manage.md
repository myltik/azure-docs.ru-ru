<properties
	pageTitle="Создание заданий эластичной базы данных и управление ими"
	description="Пошаговая инструкция по созданию задания эластичной базы данных и управлению им."
	services="sql-database"
	documentationCenter=""
	manager="jhubbard"
	authors="sidneyh"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="sidneyh"/>

# Создание заданий эластичной базы данных и управление ими

**Эластичные пулы базы данных** — это прогнозируемая модель для развертывания большого количества баз данных. Можно задать минимальное количество единиц пропускной способности (DTU) для каждой базы данных по установленной цене. Для управления общими объектами в этих базах данных проще всего использовать **задания эластичных баз данных**. Служба позволяет выполнять сценарии T-SQL для всех баз данных пула в рамках одной операции. Например, можно настроить политику для каждой базы данных, чтобы только пользователь с соответствующими учетными данными мог просматривать конфиденциальные данные.

## Предварительные требования

* Подписка Azure. Зарегистрироваться в пробной версии, которая доступна бесплатно в течение одного месяца, можно [здесь](http://azure.microsoft.com/pricing/free-trial/).
* Эластичный пул баз данных. См. информацию [об эластичных пулах баз данных](sql-database-elastic-pool.md)
* Установка компонентов службы заданий эластичной базы данных. См. статью [Установка компонентов службы заданий эластичной базы данных](sql-database-elastic-jobs-service-installation.md).

## Создание заданий

1. В колонке пула заданий эластичной базы данных выберите **Создать задание**.
2. Введите имя пользователя и пароль администратора (создаются во время установки заданий) управляющей базы данных заданий (хранилище метаданных для заданий).

	![Назовите задание, введите или вставьте код, а затем нажмите кнопку «Выполнить».][1]
2. В колонке **Создание задания** введите имя задания.
3. Введите имя пользователя и пароль для подключения к целевым базам данных. Эти учетные данные должны обладать достаточными разрешениями, иначе сценарий не выполнится.
4. Вставьте или введите сценарий T-SQL.
5. Нажмите **Сохранить**, затем нажмите кнопку **Запустить**.

	![Создание заданий и их выполнение][5]

## Запуск идемпотентных заданий

При запуске сценария для набора баз данных необходимо убедиться, что сценарий является идемпотентным. То есть в сценарий должна быть заложена возможность многоразового запуска, даже если он завершился сбоем до завершения выполнения. Например, при сбое сценария задание будет автоматически повторяться, пока не будет успешно выполнено (в установленных пределах, так как логика повтора в конечном итоге прекратит попытки). Чтобы сделать это, используйте предложение IF EXISTS и удалите все найденные экземпляры перед созданием нового объекта. Ниже приведен пример.

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Или используйте предложение IF NOT EXISTS перед созданием нового экземпляра:

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	 CREATE TABLE TestTable(
	  TestTableId INT PRIMARY KEY IDENTITY,
	  InsertionTime DATETIME2
	 );
	END
	GO

	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO

Этот сценарий обновляет таблицу, созданную ранее.

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN

	ALTER TABLE TestTable

	ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO


## Проверка состояния заданий

После запуска задания вы можете проверить ход его выполнения.

1. На странице эластичного пула баз данных щелкните **Управление заданиями**.

	![Щелкните «Управление заданиями».][2]

2. Щелкните имя (a) задания. **СОСТОЯНИЕ** может быть «Завершено» или «Ошибка». Сведения о задании отображаются (b) с датой и временем его создания и выполнения. В списке (c) ниже отображается ход выполнения сценария для каждой базы данных в пуле и отображаются сведения о его дате и времени.

	![Проверка завершенного задания][3]


## Проверка невыполненных заданий

Если происходит сбой задания, можно просмотреть журнал его выполнения. Щелкните имя невыполненного задания, чтобы просмотреть сведения о нем.

![Проверка невыполненного задания][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 

<!---HONumber=58_postMigration-->