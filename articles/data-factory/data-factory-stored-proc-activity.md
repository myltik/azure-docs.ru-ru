<properties 
	pageTitle="Действие ";Хранимая процедура SQL Server";" 
	description="Узнайте, как с помощью действия хранимой процедуры SQL Server можно вызвать хранимую процедуру в Базе данных SQL Azure или хранилище данных SQL Azure из конвейера фабрики данных." 
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
	ms.date="09/30/2015" 
	ms.author="spelluru"/>

# Действие "Хранимая процедура SQL Server"

C помощью действия хранимой процедуры SQL Server в [конвейере](data-factory-create-pipelines.md) фабрики данных можно вызвать хранимую процедуру в **базе данных SQL Azure** или в **хранилище данных SQL Azure**. Данная статья основана на материалах статьи о [действиях преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и список поддерживаемых действий преобразования.

## Синтаксис
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
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
inputs | Входные наборы данных, которые должны быть доступны (в состоянии "Готово") для выполнения действия хранимой процедуры. Входные данные действия хранимой процедуры используются только для управления зависимостями во время помещения этого действия в цепочку вместе с другими. Входные наборы данных не могут потребляться в хранимой процедуре в качестве параметра. | Нет
outputs | Выходные наборы данных, создаваемые действием хранимой процедуры. Убедитесь, что выходная таблица использует связанную службу, которая связывает Базу данных SQL Azure или хранилище данных SQL Azure с фабрикой данных. Выходные данные действия хранимой процедуры могут использоваться для передачи результата этого действия для дальнейшей обработки и/или для управления зависимостями во время его помещения в цепочку вместе с другими действиями. | Да
storedProcedureName | Укажите имя хранимой процедуры в базе данных SQL Azure или хранилище данных SQL Azure, представленной связанной службой, которую использует выходная таблица. | Да
storedProcedureParameters | Указываемые значения для параметров хранимой процедуры. | Нет

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

> [AZURE.NOTE]**Имя** и **регистр** параметра (в этом примере — DateTime) должны соответствовать параметру, указанному в действии JSON ниже. Убедитесь, что в определении хранимой процедуры в качестве префикса для параметра используется символ **@**.

Вот что нужно сделать, чтобы выполнить эту хранимую процедуру в конвейере фабрики данных:

1.	Создайте [связанную службу](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties) для регистрации строки подключения базы данных SQL Azure, в которой необходимо выполнить хранимую процедуру.
2.	Создайте [набор данных](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties), указывающий на выходную таблицу в базе данных SQL Azure. Назовем этот набор данных sprocsampleout. Этот набор данных должен ссылаться на связанную службу на шаге номер 1. 
3.	Создайте хранимую процедуру в базе данных SQL Azure.
4.	Создайте указанный ниже [конвейер](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties) с действием SqlServerStoredProcedure для вызова хранимой процедуры в Базе данных SQL Azure.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		            	"name": "SprocActivitySample",
		             	"type": " SqlServerStoredProcedure",
		             	"outputs": [ {"name": "sprocsampleout"} ],
		             	"typeProperties":
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
6.	[Отслеживайте состояние конвейера](data-factory-monitor-manage-pipelines.md) с помощью функций мониторинга фабрики данных и управления ею.

> [AZURE.NOTE]В приведенном выше примере SprocActivitySample не содержит входных данных. Если требуется организовать его в цепочку с вышестоящим действием (например, ранее выполнявшимся действием обработки), выходные данные вышестоящего действия можно использовать как входные данные в этом действии. В таком случае действие не будет выполнено, пока не завершится вышестоящее действие и не будут доступны выходные данные вышестоящих действий (в состоянии "Готово"). Входные данные нельзя использовать непосредственно в качестве параметра действия хранимой процедуры
> 
> Имена и регистр (верхний или нижний) параметров хранимых процедур в JSON-файле должны совпадать с именами параметров хранимой процедуры в целевой базе данных.

Теперь давайте рассмотрим добавление другого столбца с именем "Scenario" в таблицу, содержащую статическое значение с именем "Document sample".

![Пример данных 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

Чтобы выполнить это, передайте параметр Scenario и значение из действия хранимой процедуры. Раздел typeproperties в приведенном выше примере выглядит следующим образом:

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=Oct15_HO1-->