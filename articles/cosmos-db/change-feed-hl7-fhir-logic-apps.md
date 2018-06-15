---
title: Веб-канал изменений для ресурсов HL7 FHIR в Azure Cosmos DB | Документация Майкрософт
description: Сведения о настройке уведомлений об изменениях в медицинских картах пациентов в системе HL7 FHIR с помощью Azure Logic Apps, Azure Cosmos DB и служебной шины.
keywords: HL7 FHIR
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: sngun
ms.openlocfilehash: 9d05c41e7ebf9d1cc0735da8853e4ad1617eb810
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610503"
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Уведомление пациентов об изменениях в медицинских картах HL7 FHIR с помощью Logic Apps и Azure Cosmos DB

Недавно специалист MVP по Azure Ховард Эдидин (Howard Edidin) получил запрос от медицинской организации, которая решила добавить новые функциональные возможности на свой портал для пациентов. Ей нужно было отправлять пациентам уведомления об изменениях в медицинских картах, а также предоставить пациентам возможность подписаться на такие уведомления. 

Эта статья содержит пошаговое описание процесса, позволившего создать для этой организации веб-канал изменений с помощью Azure Cosmos DB, Logic Apps и служебной шины. 

## <a name="project-requirements"></a>Проектные требования
- Поставщики отправляют документы в формате XML, соответствующие архитектуре HL7 для консолидированных клинических документов (C-CDA). Документы C-CDA могут включать практически любые типы клинических документов, в том числе клинические (семейные истории болезни, журналы вакцинации и т. п.), административные, операционные и финансовые. 
- Документы C-CDA преобразуются в [ресурсы HL7 FHIR](http://hl7.org/fhir/2017Jan/resourcelist.html) в формате JSON.
- Преобразованные документы ресурсов FHIR отправляются по электронной почте в формате JSON.

## <a name="solution-workflow"></a>Рабочий процесс решения 

На высоком уровне этот проект включал следующие этапы рабочего процесса. 
1. Преобразование документов C-CDA в ресурсы FHIR.
2. Регулярный опрос триггеров для выявления измененных ресурсов FHIR. 
2. Вызов пользовательского приложения FhirNotificationApi для подключения к Azure Cosmos DB и получения новых и измененных документов.
3. Сохранение ответа в очереди служебной шины.
4. Опрос очереди служебной шины для отслеживания новых сообщений.
5. Отправка пациентам уведомлений по электронной почте.

## <a name="solution-architecture"></a>Архитектура решения
Для выполнения перечисленных выше требований и создания полного рабочего процесса в это решение потребовалось включить три приложения логики, перечисленные ниже.
1. **Приложение HL7-FHIR-Mapping** получает документ HL7 C-CDA, преобразует его в ресурс FHIR, а затем сохраняет в Azure Cosmos DB.
2. **Приложение EHR** запрашивает репозиторий FHIR в Azure Cosmos DB и сохраняет ответ в очередь служебной шины. Это приложение логики использует [приложение API](#api-app) для получения новых и измененных документов.
3. **Приложение для обработки уведомлений** отправляет по электронной почте уведомление, в текст которого включает документы ресурсов FHIR.

![Три приложения логики, используемые в этом решении HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Службы Azure, используемые в решении

#### <a name="azure-cosmos-db-sql-api"></a>API SQL для Azure Cosmos DB
Azure Cosmos DB выполняет роль репозитория для ресурсов FHIR, как показано на следующем рисунке.

![Учетная запись Azure Cosmos DB, используемая в этом руководстве по HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Приложения логики обрабатывают рабочий процесс. На следующих снимках экрана показаны приложения логики, созданные для этого решения. 


1. **Приложение HL7-FHIR-Mapping** получает документ HL7 C-CDA и преобразует его в ресурс FHIR с помощью пакета интеграции Enterprise для Logic Apps. Пакет интеграции Enterprise обрабатывает сопоставление C-CDA с ресурсами FHIR.

    ![Приложение логики для получения медицинских карт HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Приложение EHR** отправляет запросы в репозиторий FHIR в Azure Cosmos DB и сохраняет ответ в очередь служебной шины. Ниже приводится код приложения GetNewOrModifiedFHIRDocuments.

    ![Приложение логики, используемое для запросов к Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Приложение для обработки уведомлений** отправляет по электронной почте уведомление, в текст которого включает документы ресурсов FHIR.

    ![Приложение логики, отправляющее пациенту сообщение электронной почты, текст которого содержит ресурсы HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Служебная шина Azure
На следующем рисунке показана очередь пациентов. Значение свойства Tag используется для создания темы сообщения электронной почты.

![Очередь служебной шины, используемая в этом руководстве по HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>приложение API;
Приложение API подключается к Azure Cosmos DB и запрашивает наличие новых или измененных документов FHIR по типу ресурса. Это приложение содержит один контроллер **FhirNotificationApi** с одной операцией **GetNewOrModifiedFhirDocuments** (см. раздел об [исходном коде для приложения API](#api-app-source)).

Мы используем класс [`CreateDocumentChangeFeedQuery`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) из API-интерфейса .NET для SQL в Azure Cosmos DB. Дополнительные сведения см. в статье [Работа с поддержкой веб-канала изменений в Azure DocumentDB](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Операция GetNewOrModifiedFhirDocuments

**Входные данные**
- DatabaseId;
- CollectionId;
- имя типа ресурса FHIR HL7;
- логическое значение Start from Beginning (Начать с начала);
- целочисленное значение количества обработанных документов.

**Outputs**
- При успешном завершении: код состояния 200; возвращается список документов (в массиве JSON).
- В случае ошибки: код состояния 404; возвращает строку "No Documents found for '*resource name'* Resource Type" (Не найдены документы с типом ресурса "имя_ресурса").

<a id="api-app-source"></a>

**Исходный код приложения API**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Тестирование FhirNotificationApi 

На следующем рисунке показано, как использовалось приложение Swagger для тестирования [FhirNotificationApi](#api-app-source).

![Файл Swagger, используемый для тестирования приложения API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Панель мониторинга портала Azure

На следующем рисунке показаны все службы Azure, используемые в этом решении, которые работают на портале Azure.

![Портал Azure с информацией обо всех службах, используемых в этом руководстве по HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Сводка

- Вы узнали, что в Azure Cosmos DB есть встроенная поддержка уведомлений о новых или измененных документах, и научились легко ее применять. 
- Используя приложения логики, вы можете создавать рабочие процессы даже без написания кода.
- Также вы узнали, как использовать очереди служебной шины Azure для обработки распространения документов HL7 FHIR.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о базе данных Azure Cosmos DB см. на [ее главной странице](https://azure.microsoft.com/services/cosmos-db/). Подробную информацию о приложениях логики см. [здесь](https://azure.microsoft.com/services/logic-apps/).


