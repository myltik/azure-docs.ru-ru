---
title: Управление аналитикой озера данных Azure с помощью пакета SDK Azure для Node.js | Microsoft Docs
description: Узнайте, как управлять учетными записями, источниками данных, заданиями и пользователями аналитики озера данных Azure с помощью пакета SDK Azure для Node.js.
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Управление аналитикой озера данных Azure с помощью пакета SDK Azure для Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Используя пакет SDK Azure для Node.js, можно управлять учетными записями, заданиями и каталогами аналитики озера данных Azure. Для просмотра статей, посвященных управлению с помощью других средств, щелкните средство выбора вкладок выше.

Сейчас он поддерживает:

* **Node.js версии 0.10.0 или выше;**
* **версию REST API для учетной записи: 2015-10-01-preview**;
* **версию REST API для каталога: 2015-10-01-preview**;
* **версию REST API для задания: 2016-03-20-preview**.

## Функции
* Управление учетными записями: создание, получение, получение списков, обновление и удаление.
* Управление заданиями: отправка, получение, получение списков и отмена.
* Управление каталогами: получение, получение списков, создание (секретов), обновление (секретов), удаление (секретов).

## Установка
```bash
npm install azure-arm-datalake-analytics
```

## Проверка подлинности с помощью Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Создание клиента аналитики озера данных
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## Создание учетной записи аналитики озера данных
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Получение списка заданий
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Получение списка баз данных в каталоге аналитики озера данных
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Дополнительные материалы
* [Пакет Microsoft Azure SDK для Node.js](https://github.com/azure/azure-sdk-for-node)
* [Пакет Microsoft Azure SDK для Node.js — управление хранилищем озера данных](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

<!---HONumber=AcomDC_0914_2016-->