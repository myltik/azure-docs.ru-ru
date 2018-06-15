---
title: Настройка оповещений о работоспособности служб Azure для ServiceNow | Документация Майкрософт
description: Передача в экземпляр ServiceNow персонализированных уведомлений о событиях работоспособности служб.
author: shawntabrizi
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 867a8c0b478df9d2b7690b8b914ded7c42558583
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178874"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Настройка оповещений о работоспособности служб для ServiceNow

В этой статье показано, как с помощью веб-перехватчика интегрировать ServiceNow с оповещениями о работоспособности служб Azure. Если вы настроите интеграцию веб-перехватчика с экземпляром ServiceNow, в вашу привычную среду уведомлений будут поступать оповещения о проблемах со службами Azure, которые могут вас затронуть. Каждый раз, когда срабатывает оповещение службы работоспособности служб Azure, вызывается веб-перехватчик через REST API ServiceNow на основе скриптов.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Создание REST API на основе скриптов в ServiceNow
1.  Убедитесь, что у вас есть учетная запись [ServiceNow](https://www.servicenow.com/) и выполните вход в нее.

2.  Перейдите к разделу **System Web Services** (Системные веб-службы) в ServiceNow и выберите элемент **Scripted REST APIs** (REST API на основе скриптов).

    ![Раздел "Веб-служба на основе скриптов" в ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

3.  Выберите действие **Создать**, чтобы создать новую службу REST на основе скриптов.
 
    ![Кнопка "Создать новый REST API на основе скриптов" в ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

4.  Укажите **Имя** нового REST API и установите для параметра **Идентификатор API** значение `azureservicehealth`.

5.  Нажмите кнопку **Submit** (Отправить).

    ![Параметры REST API в ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  Выберите только что созданный REST API и на вкладке **Ресурсы** выберите **Создать**.

    ![Вкладка ресурсов в ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

7.  В поле **Имя** для нового ресурса введите `event`, а для параметра **Метод HTTP** укажите значение `POST`.

8.  В разделе **Скрипт** добавьте следующий код JavaScript.

    >[!NOTE]
    >В приведенном ниже скрипте следует изменить значения параметров `<secret>`,`<group>` и `<email>`.
    >* `<secret>` должен содержать произвольную строку, например уникальный идентификатор.
    >* `<group>` должен обозначать группу ServiceNow, которой вы хотите назначить этот инцидент.
    >* `<email>` должен обозначать конкретного пользователя, которому вы хотите назначить этот инцидент (необязательно).
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

9.  На вкладке "Безопасность" снимите флажки **Требуется проверка подлинности** и щелкните **Отправить**. Вместо проверки подлинности защиту этого API обеспечит настроенный вами `<secret>`.

    ![Флажок "Требуется проверка подлинности" в ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

10.  Вернитесь в раздел "REST API на основе скриптов", где указан параметр **Base API Path** (Базовый путь к API) для нового REST API.

     ![Базовый путь к API в ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

11.  Полный URL-адрес интеграции выглядит следующим образом.
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Создание предупреждения для ServiceNow на портале Azure
### <a name="for-a-new-action-group"></a>Для новой группы действий выполните следующее.
1. Выполните шаги с 1 по 8, описанные [в этой статье](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md), чтобы создать предупреждение в новой группе действий.

2. В списке **Действия** определите следующие сведения.

    a. **Тип действия**: *Веб-перехватчик*.

    Б. **Подробности.** **URL-адрес интеграции** для ServiceNow, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

3. Завершив создание оповещения, нажмите кнопку **Сохранить**.

### <a name="for-an-existing-action-group"></a>Для существующей группы действий сделайте следующее.
1. На [портале Azure](https://portal.azure.com/) выберите **Монитор**.

2. В разделе **Параметры** выберите **Группы действий**.

3. Найдите и выберите группу действий, для которой нужно изменить сведения.

4. В список **Действия** добавьте следующие сведения.

    a. **Тип действия**: *Веб-перехватчик*.

    Б. **Подробности.** **URL-адрес интеграции** для ServiceNow, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

5. Завершив изменение группы действий, нажмите кнопку **Сохранить**.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Проверка интеграции с веб-перехватчиком с помощью запроса HTTP POST
1. Создайте полезные данные о работоспособности служб, которые хотите отправить. Пример полезных данных для веб-перехватчика службы работоспособности служб см. в статье [Веб-перехватчики для оповещений журнала действий Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Создайте запрос HTTP POST следующим образом:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Вы должны получить ответ `200 OK` с сообщением "Incident created" (Инцидент создан).

4. Откройте [ServiceNow](https://www.servicenow.com/) и убедитесь, что интеграция настроена успешно.

## <a name="next-steps"></a>Дополнительная информация
- Узнайте, [как настроить уведомления веб-перехватчика для существующих систем управления проблемами](service-health-alert-webhook-guide.md).
- Просмотрите схему веб-перехватчика оповещений журнала действий в статье [Объекты webhook для оповещений журнала действий Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../monitoring-and-diagnostics/monitoring-action-groups.md).