---
title: Интеграция службы автоматизации Azure со службой "Сетка событий Azure" | Документация Майкрософт
description: Узнайте, как автоматически добавить тег при создании новой виртуальной машины и как отправить уведомление в Microsoft Teams.
keywords: automation, runbook, teams, event grid, virtual machine, VM
services: automation
documentationcenter: ''
author: eamonoreilly
manager: ''
editor: ''
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: eamono
ms.openlocfilehash: 9a4d6ecf19fc96a9c7b92cf246effbf3948fb478
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
ms.locfileid: "26349076"
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Интеграция службы автоматизации Azure со службой "Сетка событий Azure" и Microsoft Teams

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * импорт примера runbook для Сетки событий;
> * создание перехватчика для Microsoft Teams (необязательно);
> * создание веб-перехватчика для runbook;
> * создание подписки в службе "Сетка событий";
> * создание виртуальной машины, которая запускает runbook.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить инструкции из этого руководства, вам понадобится [учетная запись службы автоматизации Azure](../automation/automation-offering-get-started.md), в которой будет храниться runbook для запуска из подписки служба "Сетка событий".

## <a name="import-an-event-grid-sample-runbook"></a>Импорт примера runbook для службы "Сетка событий"
1. Выберите учетную запись службы автоматизации и перейдите на страницу **Runbooks**.

   ![Выбор модулей runbook](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Нажмите кнопку **Просмотреть коллекцию**.

3. Выполните поиск по запросу **Сетка событий** и выберите элемент **Integrating Azure Automation with Event grid** (Интеграция службы автоматизации Azure с Сеткой событий). 

    ![Импорт runbook из коллекции](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Выберите действие **Импорт** и присвойте ему имя **Watch-VMWrite**.

5. Когда импорт успешно завершится, выберите **Изменить**, чтобы просмотреть источник runbook. Нажмите кнопку **Опубликовать**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Создание перехватчика для Microsoft Teams (необязательно)
1. В Microsoft Teams выберите элемент **Дополнительные параметры** рядом с именем канала, а затем выберите **Соединители**.

    ![Подключения Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Прокрутите список соединителей до элемента **Входящий веб-перехватчик** и выберите для него действие **Добавить**.

3. Введите имя **AzureAutomationIntegration** и нажмите кнопку **Создать**.

4. Скопируйте веб-перехватчик в буфер обмена и сохраните его. URL-адрес веб-перехватчика используется для отправки данных в Microsoft Teams.

5. Выберите **Готово**, чтобы сохранить веб-перехватчик.

## <a name="create-a-webhook-for-the-runbook"></a>Создание веб-перехватчика для runbook
1. Откройте runbook с именем Watch-VMWrite.

2. Выберите **Веб-перехватчики** и нажмите кнопку **Добавить веб-перехватчик**.

3. Введите имя **WatchVMEventGrid**. Скопируйте URL-адрес в буфер обмена и сохраните его.

    ![Настройка имени веб-перехватчика](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Выберите действие **Настройка параметров и настроек запуска** и введите URL-адрес веб-перехватчика Microsoft Teams в поле **CHANNELURL**. Поле **WEBHOOKDATA** оставьте пустым.

    ![Настройка параметров веб-перехватчика](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Выберите **ОК**, чтобы создать веб-перехватчик для runbook службы автоматизации.


## <a name="create-an-event-grid-subscription"></a>Создание подписки Сетки событий
1. На странице **Учетная запись службы автоматизации** выберите элемент **Сетка событий**.

    ![Выбор службы "Сетка событий"](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Нажмите кнопку **+ Подписка на события**.

3. Настройте для подписки следующие сведения.

    *   Введите имя **AzureAutomation**.
    *   В списке **Тип раздела** выберите вариант **Подписки Azure**.
    *   Снимите флажок **Подписка на все типы событий**.
    *   В списке **Типы событий** выберите вариант **Resource Write Success** (Успешная запись ресурсов).
    *   В поле **Конечная точка подписчика** введите URL-адрес веб-перехватчика для runbook с именем Watch-VMWrite.
    *   В поле **Фильтр префиксов** введите подписку и группу ресурсов, в которых нужно проверять новые виртуальные машины. Результат будет выглядеть так: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`.

4. Выберите действие **Создать**, чтобы сохранить подписку Сетки событий.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Создание виртуальной машины, которая запускает runbook
1. Создайте новую виртуальную машину в той группе ресурсов, которую вы указали в фильтре префиксов для подписки Сетки событий.

2. Если все настроено правильно, runbook Watch-VMWrite сработает и добавит к этой виртуальной машине новый тег.

    ![Тег виртуальной машины](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Новое сообщение отправляется в канал Microsoft Teams.

    ![Уведомление для Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы настроили интеграцию между Сеткой событий и службой автоматизации. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * импорт примера runbook для Сетки событий;
> * создание перехватчика для Microsoft Teams (необязательно);
> * создание веб-перехватчика для runbook;
> * создание подписки в службе "Сетка событий";
> * создание виртуальной машины, которая запускает runbook.

> [!div class="nextstepaction"]
> [Создание и перенаправление пользовательского события при помощи службы "Сетка событий"](../event-grid/custom-event-quickstart.md)
