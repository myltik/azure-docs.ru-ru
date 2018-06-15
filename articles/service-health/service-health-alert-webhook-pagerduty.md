---
title: Настройка оповещений о работоспособности служб Azure для PagerDuty | Документация Майкрософт
description: Передача в экземпляр PagerDuty персонализированных уведомлений о событиях работоспособности служб.
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
ms.openlocfilehash: 6e9fcf20d368e270f9af4551c539acd873335498
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178918"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Настройка оповещений о работоспособности служб для PagerDuty

В этой статье показано, как с помощью веб-перехватчика настроить в PagerDuty уведомления о работоспособности служб Azure. Используя настраиваемый тип интеграции Microsoft Azure в [PagerDuty](https://www.pagerduty.com/), можно без особых усилий добавить оповещения о работоспособности служб в новые или существующие службы PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Создание URL-адреса интеграции работоспособности служб в PagerDuty
1.  Убедитесь, что у вас есть учетная запись [PagerDuty](https://www.pagerduty.com/), и выполните вход в нее.

2.  Перейдите в раздел **Services** (Службы) в PagerDuty.

    ![Раздел Службы (Службы) в PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Выберите **Add New Service** (Добавить новую службу) или откройте существующую настроенную службу.

4.  В разделе **Integration Settings** (Параметры интеграции) выберите следующее:

    a. **Integration Type** (Тип интеграции): Microsoft Azure.

    Б. **Integration Name** (Имя интеграции): \<имя\>.

    ![Раздел параметров интеграции в PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  Заполните остальные обязательные поля и выберите **Add** (Добавить).

6.  Откройте созданную новую интеграцию, а затем скопируйте и сохраните **URL-адрес интеграции**.

    ![URL-адрес интеграции в PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Создание оповещения с помощью PagerDuty на портале Azure
### <a name="for-a-new-action-group"></a>Для новой группы действий сделайте следующее.
1. Выполните шаги 1–8 из раздела [Создание оповещения для уведомления о работоспособности службы для новой группы действий с помощью портала Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. В списке **Действия** определите следующие сведения.

    a. **Тип действия**: *Веб-перехватчик*.

    Б. **Подробности**: **URL-адрес интеграции** для PagerDuty, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

3. Завершив создание оповещения, нажмите кнопку **Сохранить**.

### <a name="for-an-existing-action-group"></a>Для существующей группы действий сделайте следующее.
1. На [портале Azure](https://portal.azure.com/) выберите **Монитор**.

2. В разделе **Параметры** выберите **Группы действий**.

3. Найдите и выберите группу действий, для которой нужно изменить сведения.

4. В список **Действия** добавьте следующие сведения.

    a. **Тип действия**: *Веб-перехватчик*.

    Б. **Подробности**: **URL-адрес интеграции** для PagerDuty, который вы сохранили ранее.

    c. **Имя**: имя, псевдоним или идентификатор веб-перехватчика.

5. Завершив изменение группы действий, нажмите кнопку **Сохранить**.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Проверка интеграции с веб-перехватчиком с помощью запроса HTTP POST
1. Создайте полезные данные о работоспособности служб, которые хотите отправить. Пример полезных данных для веб-перехватчика службы работоспособности служб см. в статье [Веб-перехватчики для оповещений журнала действий Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Создайте запрос HTTP POST следующим образом:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Вы должны получить `202 Accepted` с сообщением, содержащим идентификатор события.

4. Откройте [PagerDuty](https://www.pagerduty.com/) и убедитесь, что интеграция настроена успешно.

## <a name="next-steps"></a>Дополнительная информация
- Узнайте, [как настроить уведомления веб-перехватчика для существующих систем управления проблемами](service-health-alert-webhook-guide.md).
- Просмотрите схему веб-перехватчика оповещений журнала действий в статье [Объекты webhook для оповещений журнала действий Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../monitoring-and-diagnostics/monitoring-action-groups.md).