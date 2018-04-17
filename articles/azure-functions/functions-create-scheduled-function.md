---
title: Создание функции, которая выполняется по расписанию, в Azure | Документация Майкрософт
description: Узнайте, как создать в Azure функцию, которая выполняется по определенному расписанию.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9c961c06a01bba0b645088a19cd55e72a54396c1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Создание в Azure функции, активируемой по таймеру

Узнайте, как создать [независимую от сервера](https://azure.microsoft.com/overview/serverless-computing/) функцию, которая выполняется на основе определенного расписания с помощью Функций Azure.

![Создание приложения-функции на портале Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

+ Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Приложение-функция успешно создана.](./media/functions-create-first-azure-function/function-app-create-success.png)

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Создание функции, активируемой по таймеру

1. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом **Функции**. Если это первая функция в приложении-функции, выберите **Пользовательская функция**. Откроется полный набор шаблонов функций.

    ![Страница быстрого начала работы с функциями на портале Azure](./media/functions-create-scheduled-function/add-first-function.png)

2. В поле поиска введите `timer` и выберите нужный язык для шаблона триггера таймера. 

    ![Выбор шаблона для активации функции по таймеру](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

3. Настройте новый триггер с параметрами, как указано в таблице под изображением.

    ![Создайте функцию, активируемую по таймеру, на портале Azure.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Параметр | Рекомендуемое значение | Описание |
    |---|---|---|
    | **Имя** | значение по умолчанию | Определяет имя функции, активируемой по таймеру. |
    | **[Расписание](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | [Выражение CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) с шестью полями, в котором запланировано ежеминутное выполнение функции. |

2. Нажмите кнопку **Создать**. Будет создана функция на выбранном вами языке, которая будет выполняться каждую минуту.

3. Проверьте выполнение, просмотрев записанные в журналах сведения трассировки.

    ![Средство просмотра журналов Функций на портале Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Теперь потребуется изменить расписание функции, чтобы она выполнялась раз в час, а не раз в минуту. 

## <a name="update-the-timer-schedule"></a>Обновление расписания таймера

1. Разверните вашу функцию и щелкните **Интеграция**. Здесь вы определяете входные и выходные привязки для вашей функции, а также задаете расписание. 

2. Введите в поле **Расписание** новое часовое значение `0 0 */1 * * *`, а затем щелкните **Сохранить**.  

![Обновление расписания таймера функций на портале Azure](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Теперь функция будет выполняться раз в час. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы создали функцию, которая выполняется на основе расписания.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о триггерах см.в статье [Настройка триггеров для выполнения кода с помощью Функций Azure](functions-bindings-timer.md).
