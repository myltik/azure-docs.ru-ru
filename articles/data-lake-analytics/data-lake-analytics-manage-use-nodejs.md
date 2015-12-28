<properties 
   pageTitle="Управление аналитикой озера данных Azure с помощью пакета SDK Azure для Node.js | Azure" 
   description="Узнайте, как управлять учетными записями, источниками данных, заданиями и пользователями аналитики озера данных Azure с помощью пакета SDK Azure для Node.js." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Управление аналитикой озера данных Azure с помощью пакета SDK Azure для Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Используя пакет SDK Azure для Node.js, можно управлять учетными записями, заданиями и каталогами аналитики озера данных Azure. Для просмотра статей, посвященных управлению с помощью других средств, щелкните вкладку выделения выше.

**Предварительные требования**

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Учетная запись аналитики озера данных Azure**. См. учебник [Начало работы с аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-get-started-portal.md), чтобы создать учетную запись.
- **Субъект-служба с разрешениями для доступа к учетной записи аналитики озера данных**. См. раздел [Проверка подлинности субъекта-службы в диспетчере ресурсов Azure](resource-group-authenticate-service-principal.md).

## Установка пакета SDK

Чтобы установить пакет SDK, выполните следующее.

1. Установите [Node.js](https://nodejs.org/).
2. В командной строке, окне терминала или окне Bash выполните следующие команды.

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics
	
## Пример Node.js

Следующий пример возвращает список заданий для указанной учетной записи аналитики озера данных Azure.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';
	
	var accountName = 'adla_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
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
		
			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');
	
			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##См. также 

- [Пакет Azure SDK для Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
- [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
- [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-use-portal.md)
- [Мониторинг заданий аналитики озера данных Azure и устранение связанных с ними неполадок с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_1217_2015-->