<properties 
	pageTitle="Запуск скрипта U-SQL в аналитике озера данных Azure из фабрики данных Azure" 
	description="Узнайте, как обрабатывать данные с помощью скриптов U-SQL в службе вычислений аналитики озера данных Azure." 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Запуск скрипта U-SQL в аналитике озера данных Azure из фабрики данных Azure 
Конвейер в фабрике данных Azure обрабатывает данные в связанной службе хранилища с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. В этой статье описывается **действие U-SQL в аналитике озера данных**, которое запускает скрипт **U-SQL** в связанной службе вычислений **аналитики озера данных Azure**.

> [AZURE.NOTE]Перед созданием конвейера с действием U-SQL в аналитике озера данных Azure нужно создать учетную запись аналитики озера данных Azure. Дополнительные сведения об аналитике озера данных Azure см. в статье [Начало работы с аналитикой озера данных Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> В руководстве [Построение первого конвейера](data-factory-build-your-first-pipeline.md) подробно описаны процессы создания фабрики данных, связанных служб, наборов данных и конвейера. Для создания сущностей фабрики данных запустите предложенные фрагменты кода JSON в редакторе фабрики данных, в Visual Studio или в Azure PowerShell.

## Связанная служба аналитики озера данных Azure
Можно создать связанную службу **аналитики озера данных Azure**, чтобы связать службу вычислений аналитики озера данных Azure с фабрикой данных Azure перед тем, как использовать действие U-SQL в аналитике озера данных в конвейере.

В следующем примере представлено определение JSON для связанной службы аналитики озера данных Azure.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


В следующей таблице приведены описания свойств из определения JSON.

Свойство | Описание | Обязательно
-------- | ----------- | --------
Тип | Свойству type необходимо присвоить значение **AzureDataLakeAnalytics**. | Да
accountName | Имя учетной записи аналитики озера данных Azure. | Да
dataLakeAnalyticsUri | Универсальный код ресурса (URI) аналитики озера данных Azure. | Нет 
authorization | Код авторизации извлекается автоматически после нажатия кнопки **Авторизовать** в редакторе фабрики данных и выполнения входа с авторизацией OAuth. | Да 
subscriptionId | Идентификатор подписки Azure | Нет (если не указан, используется подписка фабрики данных). 
имя\_группы\_ресурсов | Имя группы ресурсов Azure | Нет (если не указано, используется группа ресурсов фабрики данных).
sessionid | Идентификатор сеанса из сеанса авторизации OAuth. Каждый идентификатор сеанса является уникальным и используется только один раз. Это свойство создается автоматически в редакторе фабрики данных. | Да

Срок действия кода авторизации, созданного с помощью кнопки **Авторизовать**, через некоторое время истекает. Сроки действия для различных типов учетных записей пользователей см. в следующей таблице. При истечении **срока действия маркера** проверки подлинности может появиться следующее сообщение об ошибке: "Ошибка операции с учетными данными: invalid\_grant - AADSTS70002: ошибка при проверке учетных данных. AADSTS70008: срок действия предоставленных прав доступа истек или они были отозваны. Идентификатор отслеживания: d18629e8-af88-43c5-88e3-d8419eb1fca1 Идентификатор корреляции: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Временная отметка: 2015-12-15 21:09:31Z".

 
| Тип пользователя | Срок действия |
| :-------- | :----------- | 
| Пользователь, не принадлежащий к AAD (@hotmail.com, @live.com и т. д.) | 12 часов |
| Пользователь AAD и источник на основе OAuth находятся на другом [клиенте](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) по отношению к клиенту фабрики данных пользователя. | 12 часов |
| Пользователь AAD и источник на основе OAuth находятся на том же клиенте по отношению к клиенту фабрики данных пользователя. | <p> Не более 90 дней, если пользователь выполняет срезы на основе источника связанной службы на основе OAuth не реже одного раза в 14 дней. </p><p>Если в течение 90 дней пользователь не выполнил ни одного среза для этого источника в течение 14 дней, то срок действия его учетных данных истечет через 14 дней после последнего среза.</p> | 

Чтобы избежать этой ошибки или исправить ее, вам потребуется повторно авторизоваться с помощью кнопки **Авторизовать** и повторно развернуть связанную службу, когда **срок действия маркера истечет**. Значения свойств **sessionId** и **authorization** также можно задавать программно с помощью кода, приведенного в следующем разделе.

  
### Программное создание значений свойств sessionId и authorization 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Подробные сведения о классах фабрики данных, используемых в коде, см. в разделах [Класс AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Класс AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) и [Класс AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Для использования класса WindowsFormsWebAuthenticationDialog необходимо добавить ссылку на Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll.
 
 
## Действие U-SQL в аналитике озера данных 

В следующем фрагменте кода JSON определяется конвейер с действием U-SQL в аналитике озера данных. Определение действия содержит ссылку на созданную ранее связанную службу аналитики озера данных Azure.
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


В следующей таблице описаны имена и описания свойств, относящихся к этому действию.

Свойство | Описание | Обязательно
:-------- | :----------- | :--------
type | Для свойства type нужно задать значение **DataLakeAnalyticsU-SQL**. | Да
scriptPath | Путь к папке, содержащей скрипт U-SQL | Нет (если используется скрипт)
scriptLinkedService | Связанная служба, которая связывает хранилище, содержащее скрипт, с фабрикой данных | Нет (если используется скрипт)
script | Указание скрипта непосредственно в строке вместо использования scriptPath и scriptLinkedService. Например: "script" : "CREATE DATABASE test" | Нет (при использовании scriptPath и scriptLinkedService)
degreeOfParallelism | Максимальное количество узлов, которые будут использоваться одновременно для выполнения задания. | Нет
priority | Определяет, какие задания из всех в очереди должны запускаться в первую очередь. Чем меньше число, тем выше приоритет. | Нет 
parameters | Параметры скрипта U-SQL | Нет 

Определение сценария см. в разделе [Определение сценария SearchLogProcessing.txt](#script-definition).

### Примеры входных и выходных наборов данных

#### Входной набор данных
В этом примере входной набор данных находится в хранилище озера данных Azure (файл SearchLog.tsv в папке datalake/input).

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### Выходной набор данных
В этом примере выходные данные, порождаемые скриптом U-SQL, сохраняются в хранилище озера данных Azure (папка datalake/output).

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Пример связанной службы хранилища озера данных Azure
Вот определение примера связанной службы хранилища озера данных Azure, используемой наборами входных и выходных данных выше.

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

Описания свойств JSON в связанной службе хранилища озера данных Azure и фрагментах кода JSON с наборами данных выше см. в статье [Перемещение данных в хранилище озера данных Azure и из него](data-factory-azure-datalake-connector.md).

### Определение сценария

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Значения параметров **@in** и **@out** в приведенном выше сценарии U-SQL передаются динамически с помощью ADF, с использованием раздела "Параметры". См. раздел "Параметры" выше в определении конвейера.

В определении канала для заданий, которые запускаются в службе аналитики озера данных Azure, можно определить другие свойства, такие как степень параллелизма, приоритет и т. д.

<!---HONumber=AcomDC_0121_2016-->