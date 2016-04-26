<properties 
   pageTitle="Управление хранилищем озера данных Azure с помощью пакета SDK Azure для Node.js | Azure" 
   description="Узнайте, как управлять учетными записями и файловой системой хранилища озера данных." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Управление хранилищем озера данных Azure с помощью пакета SDK Azure для Node.js

> [AZURE.SELECTOR]
- [Портал](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
- [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
- [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
- [Интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Используя пакет SDK Azure для Node.js, можно управлять учетными записями и файловой системой хранилища озера данных Azure.

- Управление учетными записями: создание, получение, вывод списка, обновление и удаление.
- Управление файловой системой: создание, получение, передача, добавление, скачивание, чтение, удаление и вывод списка список.

**Предварительные требования**

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Учетная запись хранилища озера данных Azure**. См. учебник [Начало работы с хранилищем озера данных Azure с помощью портала Azure](data-lake-store-get-started-portal.md), чтобы создать учетную запись.
- **Субъект-служба с разрешениями для доступа к учетной записи аналитики озера данных**. См. раздел [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](../resource-group-authenticate-service-principal.md).

## Установка пакета SDK

Чтобы установить пакет SDK, выполните следующее.

1. Установите [Node.js](https://nodejs.org/).
2. В командной строке, окне терминала или окне Bash выполните следующие команды.

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Пример Node.js

Следующий пример создает файл в учетной записи хранилища озера данных и затем добавляет данные.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##См. также 

- [Пакет Azure SDK для Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Управление аналитикой озера данных Azure с помощью Node.js](../data-lake-analytics/data-lake-analytics-manage-use-nodejs.md)

<!---HONumber=AcomDC_0413_2016-->