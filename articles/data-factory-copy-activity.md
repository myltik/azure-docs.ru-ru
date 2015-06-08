<properties 
	pageTitle="Копирование данных с помощью фабрики данных Azure" 
	description="Узнайте, как использовать действие копирования в фабрике данных Azure, чтобы копировать данные из одного источника данных в другой источник данных." 
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
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Копирование данных с помощью фабрики данных Azure (действие копирования)
## Обзор
С помощью **действия копирования** в конвейере вы можете выполнять пакетное копирование данных из источника в приемник (место назначения). Действие копирования может использоваться в следующих сценариях:

- **Передача данных в Azure**. В этом случае данные копируются из локального источника данных (например, SQL Server) в хранилище данных Azure (например, хранилище BLOB-объектов или таблиц Azure либо база данных SQL Azure). Такие операции возможны в следующих ситуациях.
	- Сбор данных в централизованном расположении в Azure для дальнейшей обработки.
	- Перенос в Azure данных из локальной платформы или облачной платформы, отличной от Azure.
	- Архивация или резервное копирование данных в Azure для экономичного многоуровневого хранения.
- **Передача данных из Azure**. В этом случае данные копируются из Azure (например, хранилище BLOB-объектов или таблиц Azure либо база данных SQL Azure) в локальные киоски и хранилища данных (например, SQL Server). Такие операции возможны в следующих ситуациях.
	- Передача данных в локальную среду из-за отсутствия поддержки хранилища данных облака.
	- Передача данных в локальную среду, что позволит воспользоваться существующим локальным решением или инфраструктурой отчетности.
	- Архивация или резервное копирование данных в локальную среду для многоуровневого хранения.
- **Копирование внутри Azure**. В этом сценарии данные, распределенные по источникам данных Azure, объединяются в централизованное хранилище данных Azure. Например, из хранилища таблиц Azure в хранилище BLOB-объектов Azure, из хранилища BLOB-объектов в хранилище таблиц, из хранилища таблиц в базу данных SQL Azure, из хранилища BLOB-объектов в базу данных SQL Azure.

Вот несколько ссылок на ресурсы, которые позволят вам получить дополнительные сведения.

- [Основные сведения о копировании данных с помощью фабрики данных Azure][copy-activity-video] (видео).
- [Приступая к работе с фабрикой данных Azure][adfgetstarted] — учебник по копированию данных из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью действия копирования. 
- [Использование конвейера для работы с локальными данными][use-onpremises-datasources] — пошаговое руководство по копированию данных из локальной базы данных SQL Server в хранилище BLOB-объектов Azure с помощью действия копирования.


## Поддерживаемые источники и приемники
Действие копирования можно использовать в следующих случаях:

- копирование данных из хранилища BLOB-объектов в другое хранилище BLOB-объектов Azure, хранилище таблиц Azure, базу данных SQL Azure, на локальный сервер SQL Server или сервер SQL Server в IaaS;
- копирование данных из базы данных SQL Azure в хранилище BLOB-объектов Azure, хранилище таблиц Azure, базу данных SQL Azure, на локальный сервер SQL Server или сервер SQL Server в IaaS;
- копирование данных из хранилища таблиц Azure в хранилище BLOB-объектов Azure, другое хранилище таблиц Azure или базу данных SQL Azure;
- копирование данных из локального сервера SQL Server или сервера SQL Server в IaaS в хранилище BLOB-объектов Azure или базу данных SQL Azure;
- копирование данных из локальной базы данных Oracle в хранилище BLOB-объектов Azure;
- копирование данных из локальной файловой системы в хранилище BLOB-объектов Azure.
 

<table border="1">	
	<tr>
		<th><i>Источник/приемник<i></th>
		<th>BLOB-объект Azure</th>
		<th>Таблица Azure</th>
		<th>База данных SQL Azure</th>
		<th>Локальный сервер SQL Server</th>
		<th>Сервер SQL Server в IaaS</th>
	</tr>	

	<tr>
		<td><b>BLOB-объекты Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Таблицы Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>База данных SQL Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>


	<tr>
		<td><b>Локальный сервер SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Сервер SQL Server в IaaS</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Локальная файловая система</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Локальная база данных Oracle</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>


</table>

### SQL на IaaS (инфраструктура как услуга)
SQL Server в IaaS также поддерживается в качестве источника и приемника. Шлюз управления данными необходим при создании связанного службы для SQL Server в IaaS. Следует устанавливать шлюз управления данными на виртуальной машине, кроме одного размещения SQL Server, чтобы избежать снижения производительности из-за SQL Server и шлюз конкурировать за ресурсы. Дополнительные сведения о шлюзе управления данными см. в статье [Использование конвейера для работы с локальными данными][use-onpremises-datasources].

1.	Виртуальная машина с общедоступным DNS-именем и статическим открытым портом: сопоставление частного порта
2.	Виртуальная машина с общедоступным DNS-именем без предоставления конечной точки SQL
3.	Виртуальная сеть
	<ol type='a'>
<li>Облачная виртуальная частная сеть (VPN) Azure со следующей топологией в конце списка. </li>	
<li>Виртуальная машина с VPN типа «сеть-сеть» для передачи из локальной среды в облако с использованием виртуальной сети Azure.</li>	
</ol>![Фабрика данных с действием копирования][image-data-factory-copy-actvity]

## Действие копирования — компоненты
Вот какие компоненты содержит действие копирования:

- **Входная таблица**. Таблица является набором данных, который содержит схему и имеет прямоугольную форму. Этот компонент описывает входные данные для действия, в частности имя таблицы, ее тип и связанную службу, которая ссылается на источник входных данных.
- **Выходная таблица**. Выходная таблица описывает выходные данные для действия, включая имя таблицы, тип таблицы и связанную службу, которая ссылается на источник данных, содержащий выходные данные.
- **Правила преобразования**. Правила преобразования указывают, как входные данные извлекаются из источника, как выходные данные загружаются в приемник и т. д.
 
Действие копирования может иметь одну **входную** и одну **выходную** таблицу.

## <a name="CopyActivityJSONSchema"></a>JSON для действия копирования
Конвейер состоит из одного или нескольких действий. Действия в конвейерах определяются в разделе **activities[]**. Вот как выглядит JSON для конвейера:
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Каждое действие в разделе **activities** имеет следующую структуру верхнего уровня. Свойству **type** необходимо присвоить значение **CopyActivity**. Действие копирования может иметь только одну входную и одну выходную таблицы.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

В следующей таблице описаны теги, используемые с разделом действия.

<table border="1">	
	<tr>
		<th align="left">Тег</th>
		<th align="left">Описание</th>
		<th align="left">Обязательно</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Имя действия.</td>
		<td>Да</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Текст, описывающий, для чего используется действие.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Задает тип действия. <br/><br/>Для <b>type</b> необходимо задать значение <b>CopyActivity</b>.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Входные таблицы, используемые действием. Укажите только одну входную таблицу для действия копирования.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Выходные таблицы, используемые действием. Укажите только одну выходную таблицу для действия копирования.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Свойства преобразования зависят от типа. Для <b>действия копирования</b> необходимо указать разделы <b>source</b> и <b>sink</b> (в разделе <b>transformation</b>). Дополнительная информация приведена далее в этой статье. </td>
		<td>Да</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Политики, которые влияют на поведение во время выполнения действия. Если не указано, будут использоваться значения по умолчанию.</td>
		<td>Нет</td>
	</tr>


</table>

Подробную информацию о свойствах и тегах JSON см. в [справочнике по сценариям JSON][json-script-reference].

### Типы источников и приемников
Список типов и свойств источников и приемников, которые поддерживаются этими типами, см. в библиотеке MSDN в статье [Поддерживаемые источники и приемники][msdn-supported-sources-sinks].

## Действие копирования — пример
В этом примере входная и выходная таблицы определяются и используются в действии копирования внутри конвейера, копирующего данные из локальной базы данных SQL Server в большой двоичный объект Azure.

**Предположения**. В приведенных ниже примерах сценариев JSON используются ссылки на такие артефакты фабрики данных Azure:

* группа ресурсов с именем **ADF**;
* фабрика данных Azure с именем **CopyFactory**;
* связанная служба с именем **MyOnPremisesSQLDB**, указывающая на локальную базу данных SQL Server;
* связанная служба с именем **MyAzureStorage**, которая указывает на хранилище BLOB-объектов Azure.

### JSON входной таблицы
Следующий скрипт JSON определяет входную таблицу, которая ссылается на таблицу SQL **MyTable** в локальной базе данных SQL Server, которую определяет связанная служба **MyOnPremisesSQLDB**. Обратите внимание, что **name** — это имя таблицы фабрики данных Azure, а **tableName** — это имя таблицы SQL в базе данных SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

В следующем примере используется команда Azure PowerShell **New-AzureDataFactoryTable**, которая использует JSON-файл, содержащий приведенный выше сценарий, для создания таблицы (**MyOnPremTable**) в фабрике данных Azure (**CopyFactory**).
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

Подробную информацию о командлетах фабрики данных см. в [справочнике по командлетам][cmdlet-reference].

### JSON выходной таблицы
Следующий скрипт JSON определяет выходную таблицу **MyDemoBlob**, которая ссылается на BLOB-объект Azure **MyBlob** в папке BLOB-объектов **MySubFolder** в контейнере BLOB-объектов **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

В следующем примере используется команда Azure PowerShell **New-AzureDataFactoryTable**, которая использует JSON-файл, содержащий приведенный выше сценарий, для создания таблицы (**MyDemoBlob**) в фабрике данных Azure (**CopyFactory**).
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


### JSON конвейера (с действием копирования)
В этом примере конвейер **CopyActivityPipeline** определяют следующие свойства:

- свойству **type** присваивается значение **CopyActivity**;
- свойство **MyOnPremTable** определяется как входные данные (тег **inputs**);
- свойство **MyAzureBlob** определяется как выходные данные (тег **outputs**).
- В разделе **Transformation** есть два подраздела: **source** и **sink**. Для источника (source) задан тип **SqlSource**, а для приемника (sink) — тип **BlobSink**. **sqlReaderQuery** определяет преобразование (проекцию), которое необходимо выполнить в источнике. Подробную информацию обо всех свойствах см. в [справочнике по сценариям JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}


 В следующем примере используется команда Azure PowerShell **New-AzureDataFactoryPipeline**, которая использует JSON-файл, содержащий приведенный выше сценарий, для создания конвейера (**CopyActivityPipeline**) в фабрике данных Azure (**CopyFactory**).
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]Дополнительные примеры использования действия копирования см. в статье [Примеры использования действия копирования в фабрике данных Azure][copy-activity-examples].

## Безопасность
В этом разделе приведены общие инструкции по безопасности и рекомендации, которые помогут обеспечить безопасный доступ к хранилищам данных для операции копирования.

Для хранилищ данных, которые используют HTTPS-соединение, выбирайте для действия копирования HTTPS-соединение. Это позволит безопасно передавать данные по сети. Например, для **хранилища Azure** используйте в строке подключения **DefaultEndpointsProtocol = https**.

Для **базы данных SQL Azure** четко запрашивайте шифрованное соединение и не доверяйте сертификатам сервера во избежание атак «злоумышленник в середине». Для этого в строке подключения используйте **Encrypt = True** и **TrustServerCertificate = False**. Дополнительные сведения см. в статье [Безопасность в базе данных SQL Azure: рекомендации и ограничения](https://msdn.microsoft.com/library/azure/ff394108.aspx).

Для традиционных баз данных, например **SQL Server**, особенно если экземпляры находятся на виртуальной машине Azure, включите шифрование подключения. Для этого настройте подписанный сертификат, используя в строке подключения с **Encrypt = True** и **TrustServerCertificate = False**. Дополнительные сведения см. в статьях [Включение шифрования соединений в ядре СУБД (диспетчер конфигурации SQL Server)] (https://msdn.microsoft.com/library/ms191192(v=sql.110).aspx) и [Синтаксис строки подключения](https://msdn.microsoft.com/library/ms254500.aspx).

## Дополнительные возможности
- **Фильтрация столбцов с помощью определения структуры**. В зависимости от типа таблицы вы можете указать подмножество столбцов из источника, задав меньшее число столбцов в определении **Structure** определения таблицы, чем число, существующее в базовом источнике данных.
- **Правила преобразования — сопоставление столбцов**. Можно использовать сопоставление столбцов, чтобы указать, как столбцы в исходной таблице сопоставляются со столбцами в таблице приемника.
- **Обработка типов данных действием копирования**. Объясняет, в каких случаях типы данных, указанные в разделе Structure определения таблицы, принимаются, а в каких игнорируются.
- **Вызов хранимой процедуры для приемника SQL**. Для копирования данных на сервер SQL Server или в базу данных SQL Azure можно настроить и вызвать указанную пользователем хранимую процедуру.

Дополнительные сведения об этих возможностях см. в статье [Дополнительные возможности использования действия копирования в фабрике данных Azure][copy-activity-advanced].

## Пошаговые руководства
Учебник по копированию данных из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью действия копирования см. в статье [Начало работы с фабрикой данных Azure][adfgetstarted].
 
Пошаговое руководство по копированию данных из локальной базы данных SQL Server в хранилище BLOB-объектов Azure с помощью действия копирования см. в статье [Использование конвейера для работы с локальными данными][use-onpremises-datasources].

## См. также
- [Действие копирования — примеры][copy-activity-examples]
- [Основные сведения о копировании данных с помощью фабрики данных Azure][copy-activity-video] (видео)
- [Раздел о действии копирования в библиотеке MSDN][msdn-copy-activity]
- [Дополнительные возможности использования действия копирования в фабрике данных Azure][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!---HONumber=GIT-SubDir-->