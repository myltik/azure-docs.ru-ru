---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-change-feed-hl7-fhir-logic-apps
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 0b93e0cd71add8bad86c2b3c0023b524bc4f621a
ms.openlocfilehash: 1e44ae2341257df6ac367db83947178918016430
ms.lasthandoff: 02/10/2017


---
# <a name="notifications-for-new-or-changed-documentdb-resources-using-logic-apps"></a>Уведомления о новых или измененных ресурсах DocumentDB с использованием приложений логики
Эта статья была написана после того, как я увидел один вопрос, размещенный на форумах сообщества Azure DocumentDB. Вопрос звучал так: **поддерживает ли DocumentDB уведомления об измененных ресурсах**?

В течение многих лет я работал с BizTalk Server, а это очень распространенный вариант использования [адаптера LOB WCF](https://msdn.microsoft.com/library/bb798128.aspx). Поэтому я решил узнать, можно ли повторить эту функциональность в DocumentDB для новых или измененных документов.

В этой статье содержится обзор компонентов решения по уведомлениям об изменениях, в состав которого входит [триггер](documentdb-programming.md#trigger) и [приложение логики](../logic-apps/logic-apps-what-are-logic-apps.md). В документ встроены важные фрагменты кода, а все решение можно найти на сайте [GitHub](https://github.com/HEDIDIN/DocDbNotifications).

## <a name="use-case"></a>Вариант использования 
Следующая ситуация является вариантом использования в рамках этой статьи.

DocumentDB является репозиторием для документов HL7 FHIR. Предположим, что база данных DocumentDB вместе с API и приложением логики образует сервер HL7 FHIR.  Медицинское учреждение хранит данные о пациентах в безе данных DocumentDB "Пациенты". В этой базе данных имеется несколько коллекций — "Клинические исследования", "Идентификация" и т. д. Сведения о пациентах входят в коллекцию "Идентификация".  Существует коллекция с именем "Пациент".

Специалисты кардиологического отделения отслеживают данные о состоянии здоровья пациентов и назначенное лечение. Поиск новых или измененных карт пациентов занимает много времени. Медики обратились в ИТ-отдел с просьбой предоставить решение для получения уведомлений при оформлении новых медицинских карт или внесении изменений в существующие.  

ИТ-специалисты сказали, что могут создать такое решение. Кроме того, они предложили отправлять документы [в хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/) , чтобы специалисты кардиологического отделения могли легко получать к ним доступ.

## <a name="how-the-it-department-solved-the-problem"></a>Как ИТ-отдел решил проблему
Чтобы создать такое приложение, ИТ-отдел сначала принял решение его смоделировать.  Преимуществом использования нотации и модели бизнес-процессов (BPMN) является то, что эта система понятна как техническим, так и нетехническим специалистам. Весь этот процесс уведомления считается бизнес-процессом. 

## <a name="high-level-view-of-notification-process"></a>Общий обзор процесса уведомления
1. Работа начинается с приложения логики, которое содержит триггер таймера. По умолчанию триггер запускается каждый час.
2. Далее необходимо отправить HTTP-запрос POST к приложению логики.
3. Приложение логики выполняет всю работу.

![Представление высокого уровня](./media/documentdb-change-notification/high-level-view.png)

### <a name="lets-take-a-look-at-what-this-logic-app-does"></a>Задачи, выполняемые приложением логики
На следующем рисунке показаны этапы рабочего процесса приложения логики.

![Основной процесс логики](./media/documentdb-change-notification/main-logic-app-process.png)

Для этого необходимо выполнить следующие шаги:

1. Вам необходимо получить текущее значение даты и времени в формате UTC из приложения API.  Значение по умолчанию — один час назад.
2. Значение даты и времени UTC преобразуется в формат метки времени Unix. Это формат по умолчанию для меток времени в DocumentDB.
3. Вы размещаете значение в приложении API, которое выполняет запрос DocumentDB. Значение используется в запросе.
   
    ```SQL
         SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```
   
   > [!NOTE]
   > _ts представляет метаданные метки времени для всех ресурсов DocumentDB.
   > 
   > 
4. Если будут найдены какие-либо документы, текст ответа отправляется в хранилище BLOB-объектов Azure.
   
   > [!NOTE]
   > Для хранилища BLOB-объектов требуется учетная запись хранения Azure. Необходимо подготовить учетную запись хранения BLOB-объектов Azure и добавить новый BLOB-объект с именем "Пациенты". Дополнительные сведения см. в статьях [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md) и [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md).
   > 
   > 
5. И, наконец, отправляется сообщение электронной почты, которое уведомляет получателя о количестве найденных документов. Если не было найдено ни одного документа, в сообщении будет указано "Найдено документов:&0;". 

Теперь, когда вы получили общее представление о действиях рабочего процесса, давайте посмотрим, как их реализовать.

### <a name="lets-start-with-the-main-logic-app"></a>Начнем с основного приложения логики
Приложения логики доступны в [Azure Marketplace](https://portal.azure.com/), а дополнительные сведения о них можно получить в статье [Что такое приложения логики?](../logic-apps/logic-apps-what-are-logic-apps.md)

При создании приложения логики будет выведен вопрос: **какой вариант начала работы вы выберете?**

Щелкните внутри текстового поля, и вы увидите список предложений. Для этого приложения логики выберите **Вручную — при получении HTTP-запроса** , как показано ниже.

![Начало](./media/documentdb-change-notification/starting-off.png)

### <a name="design-view-of-your-completed-logic-app"></a>Представление проекта завершенного приложения логики
Пойдем дальше и посмотрим на завершенное представление проекта для приложения логики, которое называется DocDB.

![Рабочий процесс приложения логики](./media/documentdb-change-notification/workflow-expanded.png)

При изменении действий в конструкторе приложений логики вы можете выбрать **выходные данные** из HTTP-запроса или из предыдущего действия, как показано в следующем действии sendMail.

![Выбор выходных данных](./media/documentdb-change-notification/choose-outputs.png)

Перед выполнением каждого действия в рабочем процессе можно принять решение — **Добавить действие** или **Добавить условие**, как показано на следующем рисунке.

![Принятие решения](./media/documentdb-change-notification/add-action-or-condition.png)

При выборе варианта **Добавить условие**откроется форма для ввода логики, аналогичная показанной на следующем рисунке.  По сути, это бизнес-правило.  Если щелкнуть внутри поля, появится список для выбора параметров из предыдущего действия. Можно также ввести значения напрямую.

![Добавить условие](./media/documentdb-change-notification/condition1.png)

> [!NOTE]
> Кроме того, можно вводить данные в представлении кода.
> 
> 

Посмотрим на завершенное приложение логики в представлении кода.  

```JSON

       "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }

```

Если вы не знаете, что представляют разные разделы в коде, обратитесь к документации по [языку определения рабочего процесса приложения логики](http://aka.ms/logicappsdocs) .

Для этого рабочего процесса используется [триггер веб-перехватчика HTTP](https://sendgrid.com/blog/whats-webhook/). Взглянув на приведенный выше код, вы увидите параметры, аналогичные параметрам в следующем примере.

```C#

    =@{triggerBody()['Subject']}

```

`triggerBody()` представляет параметры, которые включаются в тело запроса POST REST к REST API приложения логики. `()['Subject']` представляет поле. Все эти параметры составляют текст в формате JSON. 

> [!NOTE]
> С помощью веб-перехватчика можно получить полный доступ к заголовку и тексту запроса триггера. В данном приложении вам требуется текст.
> 
> 

Как упоминалось ранее, назначить параметры можно с помощью конструктора или сделать это в представлении кода.
Если вы решили использовать представление кода, определите, каким свойствам требуются значения, как показано в следующем примере кода. 

```JSON

    "triggers": {
        "manual": {
            "inputs": {
            "schema": {
                "properties": {
            "Subject": {
                "type" : "String"    

            }
            },
                "required": [
            "Subject"
                 ],
                "type": "object"
            }
            },
            "type": "Manual"
        }
        }
```

Сейчас вы занимаетесь созданием схемы JSON, которая будет передана из текста HTTP-запроса POST.
Для срабатывания триггера потребуется URL-адрес обратного вызова.  О том, как его создать, вы узнаете позднее.  

## <a name="actions"></a>Действия
Теперь посмотрим, для чего используется каждое действие в приложении логики.

### <a name="getutcdate"></a>GetUTCDate
**Представление конструктора**

![](./media/documentdb-change-notification/getutcdate.png)

**Представление кода**

```JSON

    "GetUtcDate": {
            "conditions": [],
            "inputs": {
            "method": "get",
            "queries": {
                "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
            },
            "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
            "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
            },
            "type": "Http"
        },

```

Это действие HTTP выполняет операцию GET.  Оно вызывает метод GetUtcDate приложения API. URI использует свойство GetUtcDate_HoursBack, переданное в текст триггера.  Значение GetUtcDate_HoursBack задается в первом приложении логики. Более подробно о триггере приложения логики вы узнаете позднее в данном руководстве.

Это действие выполняет вызов приложения API, чтобы вернуть строковое значение даты в формате UTC.

#### <a name="operations"></a>Операции
**Запрос**

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
        "method": "get",
        "queries": {
          "hoursBack": "24"
        }
    }

```

**Ответ**

```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": "Fri, 15 Jan 2016 23:47:33 GMT"
    }

```

Следующим шагом является преобразование значения DateTime UTC в TimeStamp Unix, которое имеет тип .NET double.

### <a name="conversion"></a>Преобразование
##### <a name="designer-view"></a>Представление конструктора
![Преобразование](./media/documentdb-change-notification/conversion.png)

##### <a name="code-view"></a>Представление кода
```JSON

    "Conversion": {
        "conditions": [
        {
            "dependsOn": "GetUtcDate"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "currentDateTime": "@{body('GetUtcDate')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

На этом этапе вы передаете значение, возвращенное из GetUTCDate.  Здесь существует условие dependsOn, которое означает, что действие GetUTCDate должно быть успешно завершено. В противном случае это действие пропускается. 

Это действие вызывает приложение API для обработки преобразования.

#### <a name="operations"></a>Операции
##### <a name="request"></a>Запрос
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
        "method": "post",
        "queries": {
        "currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
        }
    }   
```

##### <a name="response"></a>Ответ
```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": 1452901653
    }
```

Далее необходимо выполнить операцию POST в приложении API.

### <a name="getdocuments"></a>GetDocuments
##### <a name="designer-view"></a>Представление конструктора
![Получить документ](./media/documentdb-change-notification/getdocuments.png)

##### <a name="code-view"></a>Представление кода
```JSON

    "GetDocuments": {
        "conditions": [
        {
            "dependsOn": "Conversion"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "unixTimeStamp": "@{body('Conversion')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

В действии GetDocuments вы передадите текст ответа из действия Conversion. Это параметр в URI:

```C#

    unixTimeStamp=@{body('Conversion')}

```

Действие QueryDocuments выполняет операцию POST HTTP в приложении API. 

Вызванным методом является **QueryForNewPatientDocuments**.

#### <a name="operations"></a>Операции
##### <a name="request"></a>Запрос
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
        "method": "post",
        "queries": {
        "unixTimeStamp": "1452901653"
        }
    }
```

##### <a name="response"></a>Ответ
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "Microsoft-IIS/8.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
            "status": "generated",
            "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
            {
                "use": "usual",
                "type": {
                "coding": [
                    {
                    "system": "http://hl7.org/fhir/v2/0203",
                    "code": "MR"
                    }
                ]
                },
                "system": "urn:oid:2.16.840.1.113883.19.5",
                "value": "12345"
            }
            ],
            "active": true,
            "name": [
            {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Следующим действием является сохранение документов в [хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/). 

> [!NOTE]
> Для хранилища BLOB-объектов требуется учетная запись хранения Azure. Необходимо подготовить учетную запись хранения BLOB-объектов Azure и добавить новый BLOB-объект с именем "Пациенты". Дополнительные сведения см. в статье [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md).
> 
> 

### <a name="create-file"></a>Создание файла
##### <a name="designer-view"></a>Представление конструктора
![Создание файла](./media/documentdb-change-notification/createfile.png)

##### <a name="code-view"></a>Представление кода
```JSON

    {
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Код создается из действия в конструкторе. Изменять код не требуется.

Если вы не знаете, как использовать API больших двоичных объектов Azure, то см. статью о [начале работы с API хранилища BLOB-объектов Azure](../connectors/connectors-create-api-azureblobstorage.md).

#### <a name="operations"></a>Операции
##### <a name="request"></a>Запрос
```JSON

    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### <a name="response"></a>Ответ
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:36 GMT",
        "set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
        "server": "Microsoft-HTTPAPI/2.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": {
        "Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
        "Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "LastModified": "2016-02-26T15:47:36.215Z",
        "Size": 65647,
        "MediaType": "application/octet-stream",
        "IsFolder": false,
        "ETag": "\"c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ\"",
        "FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
        }
    }
```

Последним действием является отправка уведомления по электронной почте.

### <a name="sendemail"></a>sendEmail
##### <a name="designer-view"></a>Представление конструктора
![Отправка электронного сообщения](./media/documentdb-change-notification/sendemail.png)

##### <a name="code-view"></a>Представление кода
```JSON


    "sendMail": {
        "conditions": [
        {
            "dependsOn": "GetDocuments"
        }
        ],
        "inputs": {
        "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
        "headers": {
            "Content-type": "application/x-www-form-urlencoded"
        },
        "method": "POST",
        "uri": "https://api.sendgrid.com/api/mail.send.json"
        },
        "type": "Http"
    }
```

В этом действии вы отправляете уведомление по электронной почте.  Вы используете [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29).   

Код для этого действия был создан с помощью шаблона для приложения логики и SendGrid в [101-logic-app-sendgrid Github repository](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid).

Операцией HTTP является операция POST. 

Параметры авторизации указаны в свойствах триггера.

```JSON

    },
        "sendgridPassword": {
             "type": "SecureString"
         },
         "sendgridUsername": {
            "type": "String"
         }

        In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
        },
        "toAddress": {
            "defaultValue": "XXXX@XXXX.com",
            "type": "String"
        },
        "fromAddress": {
            "defaultValue": "XXX@msn.com",
            "type": "String"
        },
        "emailBody": {
            "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
            "type": "String"
        },

```

emailBody является объединением количества документов, возвращаемых из запроса. Это может быть значение, равное нулю или больше, наряду со значением параметра "Найдено записей". Остальные параметры задаются из параметров триггера.

Это действие зависит от действия **GetDocuments** .

#### <a name="operations"></a>Операции
##### <a name="request"></a>Запрос
```JSON

    {
        "uri": "https://api.sendgrid.com/api/mail.send.json",
        "method": "POST",
        "headers": {
        "Content-type": "application/x-www-form-urlencoded"
        },
        "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
    }

```

##### <a name="response"></a>Ответ
```JSON

    {
        "statusCode": 200,
        "headers": {
        "connection": "keep-alive",
        "x-Frame-Options": "DENY,DENY",
        "access-Control-Allow-Origin": "https://sendgrid.com",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "nginx"
        },
        "body": {
        "message": "success"
        }
    }
```

Наконец, вы хотите, чтобы результаты из приложения логики отображались на портале Azure. Для этого следует добавить параметр в раздел выходных данных.

```JSON

    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
        }

```

Будет возвращено то же значение, которое отправлено в тексте сообщения электронной почты. На следующем рисунке показан пример с найденными&29; записями.

![Результаты](./media/documentdb-change-notification/logic-app-run.png)

## <a name="metrics"></a>Метрики
На портале можно настроить мониторинг для основного приложения логики. Это даст возможность просматривать задержки выполнения и другие события, как показано на следующем рисунке.

![](./media/documentdb-change-notification/metrics.png)

## <a name="docdb-trigger"></a>Триггер DocDb
Это приложение логики является триггером, который запускает рабочий процесс в основном приложении логики.

На следующем рисунке показано представление конструктора.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

    {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
        "actions": {
        "Http": {
            "conditions": [],
            "inputs": {
            "body": {
                "EmailTo": "XXXXXX@XXXXX.net",
                "GetUtcDate_HoursBack": "24",
                "Subject": "New Patients",
                "sendgridPassword": "********",
                "sendgridUsername": "azureuser@azure.com"
            },
            "method": "POST",
            "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
            },
            "type": "Http"
        }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {
        "Results": {
            "type": "String",
            "value": "@{body('Http')['status']}"
        }
        },
        "parameters": {},
        "triggers": {
        "recurrence": {
            "recurrence": {
            "frequency": "Hour",
            "interval": 24
            },
            "type": "Recurrence"
        }
        }
    }

```

Для триггера задан интервал повторения&24; часа. Действием является HTTP POST, которое использует URL-адрес обратного вызова для основного приложения логики. Текст содержит параметры, заданные в схеме JSON. 

#### <a name="operations"></a>Операции
##### <a name="request"></a>Запрос
```JSON

    {
        "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
        "method": "POST",
        "body": {
        "EmailTo": "XXXXXX@XXXXX.net",
        "GetUtcDate_HoursBack": "24",
        "Subject": "New Patients",
        "sendgridPassword": "********",
        "sendgridUsername": "azureuser@azure.com"
        }
    }

```

##### <a name="response"></a>Ответ
```JSON

    {
        "statusCode": 202,
        "headers": {
        "pragma": "no-cache",
        "x-ms-ratelimit-remaining-workflow-writes": "7486",
        "x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
        "x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
        "cache-Control": "no-cache",
        "date": "Thu, 25 Feb 2016 21:01:06 GMT"
        }
    }
```

Теперь давайте взглянем на приложение API.

## <a name="docdbnotificationapi"></a>DocDBNotificationApi
Несмотря на то, что в приложении существует несколько операций, вы будете использовать только три.

* GetUTCDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### <a name="docdbnotificationapi-operations"></a>Операции DocDBNotificationApi
Обратимся к документации по Swagger

> [!NOTE]
> Чтобы вы могли вызывать операции извне, в параметры приложения API необходимо добавить исходное значение "*" (без кавычек), разрешенное CORS, как показано на следующем рисунке.
> 
> 

![Конфигурация Cors](./media/documentdb-change-notification/cors.png)

#### <a name="getutcdate"></a>GetUTCDate
![G.](./media/documentdb-change-notification/getutcdateswagger.png)

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
![Получить дату в формате UTC](./media/documentdb-change-notification/converion-swagger.png)

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
![Запрос](./media/documentdb-change-notification/patientswagger.png)

Рассмотрим код этой операции.

#### <a name="getutcdate"></a>GetUTCDate
```C#

    /// <summary>
    /// Gets the current UTC Date value
    /// </summary>
    /// <returns></returns>
    [H ttpGet]
    [Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
    [SwaggerOperation("GetUtcDate")]
    [SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
    [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
    public string GetUtcDate(
       [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
    {


        return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
    }
```

Эта операция просто возвращает текущее значение даты и времени в формате UTC минус значение HoursBack.

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

Эта операция преобразует ответ из операции GetUtcDate в значение с типом double.

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
```C#

        /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
    }

```

Эта операция использует [пакет SDK .NET для DocumentDB](documentdb-sdk-dotnet.md) для создания запроса документа. 

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

Передается ответ из операции ConvertToTimeStamp (unixTimeStamp). Операция возвращает список документов `IList<Document>`.

Ранее мы говорили о CallbackURL. Чтобы запустить рабочий процесс в основном приложении логики, его необходимо вызвать с помощью CallbackURL.

## <a name="callbackurl"></a>CallbackURL
Для начала вам потребуется маркер Azure AD.  Его получение может оказаться трудной задачей. Мне требовался простой способ, и Джефф Холлан (Jeff Hollan), руководитель программы по приложениям логики, порекомендовал использовать [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) в PowerShell.  Чтобы его установить, следуйте приведенным инструкциям.

Вы будете использовать операции входа и вызова API ARM.

Вход — использование тех же учетных данных, что и для входа на портал Azure. 

Операция вызова API ARM используется для создания CallBackURL.

В PowerShell она вызывается следующим образом:    

```powershell

    ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

Результат должен выглядеть следующим образом:

```powershell

    https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

Для тестирования основного приложения логики можно использовать средство, такое как [postman](http://www.getpostman.com/) , как показано на рисунке ниже.

![postman](./media/documentdb-change-notification/newpostman.png)

В приведенной далее таблице перечислены параметры триггера, формирующие текст приложения логики триггера DocDB.

| Параметр | Описание |
| --- | --- |
| GetUtcDate_HoursBack |Используется для задания количества часов для даты начала поиска. |
| sendgridUsername |Используется для задания количества часов для даты начала поиска. |
| sendgridPassword |Имя пользователя для отправки сообщения "Отправить сетку". |
| EmailTo |Адрес электронной почты, на который будет отправляться уведомление. |
| Субъект |Тема сообщения электронной почты. |

## <a name="viewing-the-patient-data-in-the-azure-blob-service"></a>Просмотр данных пациентов в службе BLOB-объектов
Перейдите к учетной записи хранения Azure и в разделе служб выберите BLOB-объекты, как показано на следующем рисунке.

![Учетная запись хранения](./media/documentdb-change-notification/docdbstorageaccount.png) 

Вы сможете просмотреть сведения о файле BLOB-объекта пациента, как показано ниже.

![Служба больших двоичных объектов](./media/documentdb-change-notification/blobservice.png)

## <a name="summary"></a>Сводка
Из этого пошагового руководстве вы узнали следующее:

* в DocumentDB можно реализовать уведомления;
* процесс можно автоматизировать с помощью приложений логики;
* с помощью приложений логики можно сократить время, необходимое для доставки приложения;
* с помощью протокола HTTP можно без труда использовать приложение API в приложении логики;
* можно легко создать CallBackURL, который заменяет прослушиватель HTTP;
* с помощью конструктора приложений логики можно легко создавать настраиваемые рабочие процессы.

Главная задача — составить план и смоделировать рабочий процесс.

## <a name="next-steps"></a>Дальнейшие действия
Скачайте и используйте код приложений логики, размещенный на сайте [Github](https://github.com/HEDIDIN/DocDbNotifications). Предлагаю вам создать приложение и отправить изменения в репозиторий. 

Дополнительные сведения о DocumentDB см. на странице [схемы обучения](https://azure.microsoft.com/documentation/learning-paths/documentdb/).


