<properties 
	pageTitle="Действие «Хранимая процедура SQL Server»" 
	description="Сведения о том, как с помощью действия «Хранимая процедура SQL Server» можно вызвать хранимую процедуру в базе данных SQL Azure из конвейера фабрики данных." 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Действие «Хранимая процедура SQL Server»

С помощью действия «Хранимая процедура SQL Server» в [конвейере](data-factory-create-pipelines.md) фабрики данных можно вызвать хранимую процедуру в базе данных **SQL Azure**. Эта статья основана на материалах статьи [Действия преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

## Синтаксис
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": “”,
        	"storedProcedureParameters": “” 
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## Сведения о синтаксисе

Свойство | Описание | Обязательно
-------- | ----------- | --------
name | Имя действия. | Да
description | Текст, описывающий, для чего используется действие | Нет
type | SqlServerStoredProcedure | Да
inputs | Входные данные, которые должны быть доступны (в состоянии «Готово») для выполнения действия хранимой процедуры. | Нет
outputs | Выходные данные, создаваемые действием хранимой процедуры. Убедитесь, что выходная таблица использует связанную службу, которая связывает базу данных SQL Azure с фабрикой данных. | Да
storedProcedureName | В базе данных SQL Azure укажите имя хранимой процедуры, которая представлена связанной службой, используемой выходной таблицей. | Да
storedProcedureParameters | Указываемые значения для параметров хранимой процедуры. | Нет

> [AZURE.NOTE]Входные данные действия хранимой процедуры используются только для управления зависимостями и помещения этого действия в цепочку вместе с другими. Входные данные не могут потребляться в хранимой процедуре в качестве параметра.
 

## Пример

Рассмотрим пример, где требуется создать таблицу в базе данных SQL Azure, которая содержит два столбца.

столбец | Тип
------ | ----
ИД | uniqueidentifier
Datetime | Дата и время, когда был создан соответствующий идентификатор.

![Пример данных](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

Вот что нужно сделать, чтобы выполнить эту хранимую процедуру в конвейере фабрики данных:

1.	Создайте [связанную службу](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties) для регистрации строки подключения базы данных SQL Azure, где следует выполнить хранимую процедуру.
2.	Создайте [набор данных](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties), указывающий на выходную таблицу в базе данных SQL Azure. Назовем этот набор данных sprocsampleout. Этот набор данных должен ссылаться на связанную службу на шаге номер 1. 
3.	Создайте хранимую процедуру в базе данных SQL Azure.
4.	Создайте показанный ниже [конвейер](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties) с действием SqlServerStoredProcedure для вызова хранимой процедуры в базе данных SQL Azure.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		             "name": "SprocActivitySample",
		             "type": " SqlServerStoredProcedure ",
		             "outputs": [ {"name": "sprocsampleout"} ],
		             "typeproperties":
		              {
		                "storedProcedureName": "sp_sample",
		        		"storedProcedureParameters": 
		        		{
		            	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        		}
				}
		            }
		          ]
		     }
		}
5.	Разверните [конвейер](data-factory-create-pipelines.md).
6.	[Отслеживайте состояние конвейера](data-factory-monitor-manage-pipelines.md), используя функции мониторинга и управления фабрикой данных.

> [AZURE.NOTE]В приведенном выше примере SprocActivitySample не содержит входных данных. Если требуется организовать его в цепочку с вышестоящим действием, выходные данные вышестоящего действия можно использовать как входные данные в данном действии. В таком случае действие не будет выполнено до тих пор, пока не завершится вышестоящее действие и выходные данные не будут доступны (в состоянии «Готово»). Входные данные нельзя использовать непосредственно в качестве параметра действия хранимой процедуры
> 
> Имена и регистр (верхний или нижний) параметров хранимых процедур в JSON-файле должны совпадать с именами параметров хранимой процедуры в целевой базе данных.

Теперь давайте рассмотрим добавление другого столбца с именем «Scenario» в таблицу, содержащую статическое значение с именем «Document sample».

![Пример данных 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Чтобы выполнить это, передайте параметр Scenario и значение из действия хранимой процедуры. Раздел typeproperties в приведенном выше примере выглядит следующим образом.

	"typeproperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=August15_HO6-->