---
title: Настройка оповещений о работоспособности служб Azure для OpsGenie | Документация Майкрософт
description: Передача в экземпляр OpsGenie персонализированных уведомлений о событиях работоспособности служб.
author: shawntabrizi
manager: scotthit
editor: ''
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
ms.openlocfilehash: 6b8017f62dd895219f1d2cdac40f0efdf2db6c93
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179350"
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Настройка оповещений о работоспособности служб для OpsGenie

В этой статье показано, как с помощью веб-перехватчика настроить в OpsGenie оповещения о работоспособности служб Azure. Используя интеграцию работоспособности служб Azure в [OpsGenie](https://www.opsgenie.com/), можно перенаправлять в OpsGenie оповещения о работоспособности служб Azure. OpsGenie может определить, каких лиц следует уведомить, на основе расписаний по вызовам, используя электронную почту, текстовые сообщения (SMS), телефонные звонки, push-уведомления iOS и Android и выполняя эскалацию оповещений до их подтверждения или закрытия.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Создание URL-адреса интеграции работоспособности служб в OpsGenie
1.  Убедитесь, что у вас есть учетная запись [OpsGenie](https://www.opsgenie.com/), и выполните вход в нее.

2.  Перейдите в раздел **Integrations** (Интеграции) в OpsGenie.

    ![Раздел интеграций в OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Выберите кнопку интеграции **Azure Service Health** (Работоспособность служб Azure).

    ![Кнопка Работоспособность служб Azure (Работоспособность служб Azure) в OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  Задайте **имя** для оповещения и укажите значение в поле **Assigned to Team** (Назначено команде).

5.  Заполните остальные поля, такие как **Recipients** (Получатели), **Enabled** (Включено) и **Suppress Notifications** (Подавлять уведомления).

6.  Скопируйте и сохраните **URL-адрес интеграции**, который уже должен содержать в конце `apiKey`.

    ![URL-адрес интеграции в OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Выберите **Save Integration**(Сохранить интеграцию).

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Создание оповещения с помощью OpsGenie на портале Azure
### <a name="for-a-new-action-group"></a>Для новой группы действий сделайте следующее.
1. Выполните шаги 1–8 из раздела [Создание оповещения для уведомления о работоспособности службы для новой группы действий с помощью портала Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. В списке **Действия** определите следующие сведения.

    a. **Тип действия**: *Веб-перехватчик*.

    Б. **Подробности**: **URL-адрес интеграции** для OpsGenie, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

3. Завершив создание оповещения, нажмите кнопку **Сохранить**.

### <a name="for-an-existing-action-group"></a>Для существующей группы действий сделайте следующее.
1. На [портале Azure](https://portal.azure.com/) выберите **Монитор**.

2. В разделе **Параметры** выберите **Группы действий**.

3. Найдите и выберите группу действий, для которой нужно изменить сведения.

4. В список **Действия** добавьте следующие сведения.

    a. **Тип действия**: *Веб-перехватчик*.

    Б. **Подробности**: **URL-адрес интеграции** для OpsGenie, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

5. Завершив изменение группы действий, нажмите кнопку **Сохранить**.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Проверка интеграции с веб-перехватчиком с помощью запроса HTTP POST
1. Создайте полезные данные о работоспособности служб, которые хотите отправить. Пример полезных данных для веб-перехватчика службы работоспособности служб см. в статье [Веб-перехватчики для оповещений журнала действий Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Создайте запрос HTTP POST следующим образом:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. При его отправке должен быть получен ответ `200 OK` с сообщением о состоянии Successful (Успешно).

4. Откройте [OpsGenie](https://www.opsgenie.com/) и убедитесь, что интеграция настроена успешно.

## <a name="next-steps"></a>Дополнительная информация
- Узнайте, [как настроить уведомления веб-перехватчика для существующих систем управления проблемами](service-health-alert-webhook-guide.md).
- Просмотрите схему веб-перехватчика оповещений журнала действий в статье [Объекты webhook для оповещений журнала действий Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../monitoring-and-diagnostics/monitoring-action-groups.md).