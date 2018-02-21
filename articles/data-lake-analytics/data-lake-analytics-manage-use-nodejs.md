---
title: "Управление Azure Data Lake Analytics с помощью пакета SDK Azure для Node.js | Документация Майкрософт"
description: "Узнайте, как управлять учетными записями, источниками данных, заданиями и пользователями аналитики озера данных Azure с помощью пакета SDK Azure для Node.js."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: 769cf9b09eecd204c8b5b944065dad57a6d73231
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Управление аналитикой озера данных Azure с помощью пакета SDK Azure для Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Используя пакет SDK Azure для Node.js, можно управлять учетными записями, заданиями и каталогами аналитики озера данных Azure. Для просмотра статей, посвященных управлению с помощью других средств, щелкните средство выбора вкладок выше.

Сейчас он поддерживает:

* **Node.js версии 0.10.0 или выше;**
* **версию REST API для учетной записи: 2015-10-01-preview**
* **версию REST API для каталога: 2015-10-01-preview**
* **версию REST API для задания: 2016-03-20-preview**

## <a name="features"></a>Функции
* Управление учетными записями: создание, получение, получение списков, обновление и удаление.
* Управление заданиями: отправка, получение, перечисление и отмена.
* Управление каталогами: получение и перечисление.

## <a name="how-to-install"></a>Установка
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Проверка подлинности с помощью Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Создание клиента аналитики озера данных
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных
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

## <a name="get-a-list-of-jobs"></a>Получение списка заданий
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

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Получение списка баз данных в каталоге аналитики озера данных
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

## <a name="see-also"></a>См. также
* [Пакет Microsoft Azure SDK для Node.js](https://github.com/azure/azure-sdk-for-node)
* [Пакет Microsoft Azure SDK для Node.js — управление хранилищем озера данных](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

