<properties
    pageTitle="Ведение журнала и обработка ошибок в приложениях логики | Microsoft Azure"
    description="Пример настройки обработки ошибок и ведения журналов с помощью приложений логики для реального сценария."
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager=""
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# Ведение журнала и обработка ошибок в приложениях логики

В этой статье описано, как улучшить поддержку для обработки исключений в приложении логики. Это пример из реальной жизни, который можно считать ответом на вопрос "Поддерживает ли приложение логики обработку ошибок и исключений?"

>[AZURE.NOTE] Текущая версия функции приложений логики в службе приложений Microsoft Azure содержит стандартный шаблон для ответов на действия. В него входят внутренние проверки и обработка сообщений об ошибках, возвращаемых из приложения API.

## Вариант использования и сценарий

Следующая ситуация является вариантом использования в рамках этой статьи. Известная медицинская организация поручила нам разработать решение на базе Azure, которое позволит создать портал для пациентов с использованием Microsoft Dynamics CRM Online. Также им нужно передавать информацию о приемах между порталом пациентов на базе Dynamics CRM Online и системой Salesforce. Для передачи информации о пациентах необходимо использовать стандарт [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/).

Проект включает два основных требования:

 -  метод ведения журнала записей, отправленных с онлайн-портала Dynamics CRM Online;
 -  механизм для просмотра всех ошибок, возникающих в рамках рабочего процесса.


## Решение проблемы

>[AZURE.TIP] Общее представление о проекте вы можете получить из видео на сайте [Integration User Group](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration User Group").

В качестве репозитория для записей журнала и ошибок мы выбрали [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB.") (DocumentDB означает, что записями этой базы данных являются документы). В приложениях логики используется стандартный шаблон для всех ответов, поэтому нам не пришлось создавать настраиваемую схему. Чтобы **добавлять** и **запрашивать** записи журнала и записи об ошибках, можно было создать приложение API. В нем мы могли определить схему выполнения каждой из этих операций.

Но существовало еще одно требование: автоматическое удаление записей через определенное время. DocumentDB поддерживает свойство [Срок жизни](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Срок жизни") (TTL), которое позволяет определить значение **срока жизни** для каждой записи или всей коллекции. Это избавляет от необходимости вручную удалять записи из DocumentDB.

### Создание приложения логики

Прежде всего нужно создать приложение логики и загрузить его в конструктор. В этом примере мы используем родительское и дочерние приложения логики. Предположим, мы уже создали родительское приложение, а теперь создаем одно дочернее приложение логики.

Так как нам нужен журнал всех записей, отправляемых из Dynamics CRM Online, мы начнем с верхнего уровня. Родительское приложение логики активирует дочернее, поэтому нам нужен триггер запроса.

> [AZURE.IMPORTANT] Для работы с этим руководством потребуется создать базу данных DocumentDB и две коллекции ("Ведение журналов" и "Ошибки").

### Триггер приложения логики

Мы используем следующий триггер запроса.

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### Действия

Нам нужно записать в журнал источник (запрос) записи пациента, полученный с портала Dynamics CRM Online.

1. Мы получаем новую запись о приеме с портала Dynamics CRM Online. Триггер, полученный из CRM, содержит следующие сведения: **идентификатор пациента CRM**, **тип записи**, **состояние записи: новая или обновленная** (логическое значение) и **идентификатор Salesforce**. Параметр **SalesforceId** (идентификатор Salesforce) может иметь значение NULL, так как он используется только для обновлений. Для получения записи CRM используются параметры **PatientID** (идентификатор пациента) и **Тип записи**.
1. Далее необходимо добавить операцию **InsertLogEntry** для приложения API DocumentDB, как показано на рисунках ниже.


#### Представление конструктора для добавления записи журнала

![Добавление записи журнала](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### Представление конструктора для добавления записи об ошибке
![Добавление записи журнала](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### Проверка на ошибку создания записи

![Условие](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## Исходный код приложения логики

>[AZURE.NOTE]  Приведенный ниже код используется только в качестве примера. Так как в этом руководстве используется пример из реальной жизни, который уже реализован в рабочей среде, свойства **узла источника**, связанные с планированием приемов, могут не отображаться.

### Ведение журналов
В следующем примере кода приложения логики показано, как обрабатывать ведение журналов.

#### Запись в журнале
Вот исходный код приложения логики для добавления записи в журнал.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### Запрос к журналу

Вот сообщение с запросом к журналу, переданное в приложение API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
	    "date": "Fri, 10 Jun 2016 22:31:56 GMT",
	    "operation": "New Patient",
	    "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "providerId": "",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}"
    	}
    }

```


#### Ответ журнала

Это сообщение с ответом из журнала, полученное из приложения API.

``` json
{
    "statusCode": 200,
    "headers": {
	    "Pragma": "no-cache",
	    "Cache-Control": "no-cache",
	    "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
	    "Server": "Microsoft-IIS/8.0",
	    "X-AspNet-Version": "4.0.30319",
	    "X-Powered-By": "ASP.NET",
	    "Content-Length": "964",
	    "Content-Type": "application/json; charset=utf-8",
	    "Expires": "-1"
    },
    "body": {
	    "ttl": 2592000,
	    "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
	    "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
	    "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
	    "_ts": 1465597936,
	    "_etag": ""0400fc2f-0000-0000-0000-575b3ff00000"",
	    "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "timestamp": "2016-06-10T22:31:56Z",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}",
	    "operation": "New Patient",
	    "salesforceId": "",
	    "expired": false
    }
}

```

Теперь давайте рассмотрим действия по обработке ошибок.


### Обработка ошибок

В следующем примере кода приложения логики показано, как можно реализовать обработку ошибок.

#### Создание записи об ошибке

Это исходный код приложения логики для создания записи об ошибке.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}  	       
```

#### Добавление записи об ошибке в DocumentDB — запрос

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MasterID_mp__c":"","C_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","ONY_ID__c":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "statusCode": "400"
    }
}
```

#### Добавление записи об ошибке в DocumentDB — ответ


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": ""0c00eaac-0000-0000-0000-575b3fdc0000"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c":"DO - Degree level is DO","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MterID_mp__c":"","Medicis_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","XXXXXXX":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### Ответ на ошибку Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### Возвращение ответа в родительское приложение логики

После получения ответа его можно вернуть в родительское приложение логики.

#### Возвращение успешного ответа в родительское приложение логики

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "	Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### Возвращение ответа с ошибкой в родительское приложение логики

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## Репозиторий и портал DocumentDB

Благодаря использованию [DocumentDB](https://azure.microsoft.com/services/documentdb) наше решение предоставляет расширенные возможности.

### Портал управления ошибками

Чтобы просмотреть ошибки, полученные из DocumentDB, можно создать веб-приложение MVC, в котором будут отображаться соответствующие записи об ошибках. В текущую версию приложения MVC входят операции **List**, **Details**, **Edit** и **Delete**.

> [AZURE.NOTE] Операция Edit: DocumentDB выполняет замену всего документа. Записи в представлении списка (**List**) и в подробном представлении (**Detail**) приведены в качестве примера. Это не фактические записи о приемах пациентов.

Ниже приведены примеры информации о приложении MVC, созданном с помощью описанного выше метода.

#### Список для управления ошибками

![Список ошибок](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### Просмотр подробных сведений об ошибке

![Сведения об ошибке](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### Портал управления журналами

Для просмотра журналов мы также создали веб-приложение MVC. Ниже приведены примеры информации о приложении MVC, созданном с помощью описанного выше метода.

#### Просмотр подробных сведений об ошибке из журнала

![Просмотр сведений о записи журнала](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### Сведения о приложении API

#### API управления исключениями приложений логики

Приложение API на основе приложения логики с открытым исходным кодом, которое мы создали для управления исключениями, поддерживает следующие функциональные возможности.

Это приложение содержит два контроллера:

- **ErrorController** добавляет запись об ошибке (документ) в коллекцию DocumentDB;
- **LogController** добавляет запись журнала (документ) в коллекцию DocumentDB.

> [AZURE.TIP] Оба контроллера используют операции `async Task<dynamic>`. Это позволяет выполнять операции в среде выполнения, а значит, мы сможем создать схему DocumentDB в тексте запроса.

Каждому документу в DocumentDB необходимо присвоить уникальный идентификатор. Мы используем идентификатор `PatientId`, к которому добавляем метку времени, преобразованную в значение в формате Unix (значение с двойной точностью). Чтобы удалить дробное значение, мы обрежем метку времени.

Исходный код нашего API контроллера ошибок доступен в [репозитории GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Для вызова API из приложения логики используется следующий синтаксис.

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Выражение в примере кода выше проверяет, не имеет ли параметр *Create\_NewPatientRecord* значение **Failed**.

## Сводка

- В приложении логики можно легко реализовать ведение журнала и обработку ошибок.
- DocumentDB можно использовать в качестве репозитория для хранения записей журнала и записей об ошибках (документов).
- С помощью веб-приложения MVC можно создать портал, на котором будут отображаться записи журналов и записи об ошибках.

### Исходный код
Исходный код для приложения API, используемого для управления исключениями приложений логики, доступен в [репозитории GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API управления исключениями приложений логики").


## Дальнейшие действия
- [Примеры приложений логики и распространенные сценарии.](app-service-logic-examples-and-scenarios.md)
- [Мониторинг приложений логики.](app-service-logic-monitor-your-logic-apps.md)
- [Создание шаблона развертывания приложения логики.](app-service-logic-create-deploy-template.md)

<!---HONumber=AcomDC_0817_2016-->