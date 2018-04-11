---
title: Как расширить (копировать) оповещения с портала OMS в Azure | Документация Майкрософт
description: Инструменты и API, с помощью которых выполняется расширение оповещений из OMS в Azure, могут добровольно использоваться клиентами.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: c2e11d89f35915ef0a0c1e1f544b0be8df0473de
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Как расширить (копировать) оповещения с портала OMS в Azure
Начиная с **23 апреля 2018 г.**, все клиенты, использующие оповещения, которые настраиваются в [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), будут перемещены в Azure. Оповещения, перемещенные в Azure, функционируют так же, как и в OMS. Возможности мониторинга не изменяются. Копирование созданных в OMS оповещений в Azure предоставляет множество преимуществ. Дополнительные сведения о преимуществах и процессе копирования оповещений из OMS в Azure см. в [этой статье](monitoring-alerts-extend.md).

Пользователи, желающие переместить свои оповещения из OMS в Azure немедленно, могут сделать это с помощью одного из указанных вариантов.

## <a name="option-1---using-oms-portal"></a>Вариант 1. Использование портала OMS
Чтобы добровольно инициировать расширение оповещений с портала OMS в Azure, выполните приведенные ниже действия.

1. На странице обзора портала OMS перейдите в колонку "Параметры", а затем — в раздел "Оповещения". Нажмите кнопку Extend into Azure (Расширить в Azure), как показано ниже.

    ![Страница параметров оповещений на портале OMS с параметром расширения](./media/monitor-alerts-extend/ExtendInto.png)

2. После нажатия кнопки откроется мастер с тремя шагами. Первый шаг — предоставление сведений о процессе. Нажмите кнопку "Далее", чтобы продолжить.

    ![Расширение оповещений с портала OMS в Azure. Шаг 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. На втором шаге система предоставит сводку предлагаемого изменения для оповещений на портале OMS, перечислив соответствующие [группы действий](monitoring-action-groups.md). Если подобные действия будут выполняться для нескольких оповещений, система предложит связать их с одной группой действий.  Предлагаемая группа действий придерживается соглашения об именовании: *WorkspaceName_AG_ #Number*. Чтобы продолжить, нажмите кнопку "Далее".
Ниже представлен пример экрана.

    ![Расширение оповещений с портала OMS в Azure. Шаг 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. На последнем шаге мастера вы можете запросить на портале OMS планирование расширения всех своих оповещений в Azure, создав группы действий и связав их с оповещениями, как показано ранее. Чтобы продолжить, щелкните "Готово" и подтвердите запрос, чтобы запустить процесс. При необходимости клиенты также могут указать адреса электронной почты, на которые портал OMS будет отправлять отчет о завершении обработки.

    ![Расширение оповещений с портала OMS в Azure. Шаг 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. После завершения работы мастера вы вернетесь на страницу параметров оповещений, а параметр Extend into Azure (Расширить в Azure) будет удален. Портал OMS запланирует расширение оповещений в Azure в фоновом режиме. Этот процесс может занять некоторое время. После начала операции оповещения на портале OMS будут временно недоступны для изменения. Текущее состояние будет отображаться в заголовке, и на адреса электронной почты (если они были указаны на шаге 4) будет отправлено уведомление об успешном расширении оповещений в Azure. 

6. Оповещения будут по-прежнему отображаться на портале OMS даже после успешного расширения в Azure.

    ![Снимок экрана после расширения оповещений портала OMS в Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Вариант 2. Использование API
Пользователям, желающим программно управлять процессом расширения оповещений с портала OMS в Azure или автоматизировать его, корпорация Майкрософт предоставляет новый API AlertsVersion в Log Analytics.

К API AlertsVersion Log Analytics категории RESTful можно получить доступ с помощью REST API Azure Resource Manager. В этом документе вы найдете примеры, когда доступ к API из командной строки PowerShell осуществляется через [ARMClient](https://github.com/projectkudu/ARMClient) — инструмент командной строки с открытым кодом, который упрощает вызов API Azure Resource Manager. Использование ARMClient и PowerShell — это один из множества вариантов получения доступа к API. API выдаст результаты в формате JSON, позволяя программно использовать их различными способами.

В результате использования GET в API можно получить сводку о предлагаемом изменении в виде списка [групп действий](monitoring-action-groups.md) для оповещений портала OMS в формате JSON. Если подобные действия будут выполняться для нескольких оповещений, система предложит связать их с одной группой действий.  Предлагаемая группа действий придерживается соглашения об именовании: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> Вызов GET API не сможет выполнить расширение оповещений портала OMS в Azure. В качестве ответа он представит только сводку предлагаемых изменений. Чтобы применить эти изменения для расширения оповещений в Azure, необходимо выполнить вызов POST в API.

Если вызов GET в API вернется с ответом 200 OK, будет предоставлен список оповещений JSON вместе с предлагаемыми группами действий. Ниже приведен пример ответа.

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
В случае отсутствия оповещений в указанной рабочей области и ответа 200 OK для операции GET код JSON будет выглядеть следующим образом:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Если все оповещения в указанной рабочей области уже добавлены в Azure, ответ на вызов GET будет выглядеть следующим образом:
```json
{
    "version": 2
}
```

Чтобы инициировать планирование расширения оповещений портала OMS в Azure, инициируйте вызов POST к API. Этот вызов или команда подтверждает намерение пользователя расширить оповещения портала OMS в Azure и выполняет изменения, как указано в ответе на вызов GET к API. При необходимости пользователь может предоставить список адресов электронной почты, на которые портал OMS отправит отчет об успешном завершении запланированного фонового процесса расширения оповещений портала OMS в Azure.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Результат расширения оповещений портала OMS в Azure может отличаться от сводки изменений в системе, предоставленной вызовом GET. После планирования оповещения портала OMS будут временно недоступны для редактирования или изменения, однако можно будет создавать новые оповещения. 

Если вызов POST завершен успешно, он должен вернуть ответ 200 OK со следующим кодом:
```json
{
    "version": 2
}
```
Он указывает, что оповещения были расширены в Azure, как указано в версии 2. Эта версия нужна, чтобы проверить были ли оповещения расширены в Azure и используется ли [API поиска в службе Log Analytics](../log-analytics/log-analytics-api-alerts.md). После расширения оповещений в Azure на все адреса электронной почты, указанные при вызове GET, будет отправлен отчет с подробными сведениями о внесенных изменениях.


И наконец, если все оповещения в указанной рабочей области уже запланированы для расширения в Azure, ответ на вызов POST будет 403 (запрещено).


## <a name="next-steps"></a>Дополнительная информация

* [Изучение возможностей новой службы "Оповещения (предварительная версия)" в Azure Monitor](monitoring-overview-unified-alerts.md).
* Дополнительные сведения см. в статье [Оповещения журнала в Azure Monitor. Оповещения Azure (предварительная версия)](monitor-alerts-unified-log.md).
