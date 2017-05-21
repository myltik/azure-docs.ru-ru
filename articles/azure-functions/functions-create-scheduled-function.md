---
title: "Создание функции, которая выполняется по расписанию, в Azure | Документация Майкрософт"
description: "Узнайте, как создать в Azure функцию, которая выполняется по определенному расписанию."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Создание в Azure функции, активируемой по таймеру

Узнайте, как создать функцию, которая выполняется на основе определенного расписания с помощь Функций Azure. 

![Создание приложения-функции на портале Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

Выполнение всех шагов в этой статье займет меньше пяти минут.

## <a name="prerequisites"></a>Предварительные требования 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

В этой статье вы создадите функцию, активируемую по таймеру, в имеющемся приложении-функции. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Создание функции, активируемой по таймеру

1. Разверните свое приложение-функцию, нажмите кнопку **+** рядом с пунктом **Функции**, а затем щелкните шаблон **TimerTrigger** для нужного языка. Затем используйте настройки, указанные в таблице, и щелкните**Создать**:

    | Настройка      |  Рекомендуемое значение   | Описание                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя функции** | TimerTriggerCSharp1 | Определяет имя функции, активируемой по таймеру.
    | **[Расписание](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | [Выражение CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) с шестью полями, в котором запланировано ежеминутное выполнение функции. |

    Будет создана функция на выбранном вами языке, которая будет выполняться каждую минуту. 

4. Проверьте выполнение, просмотрев записанные в журналах сведения трассировки. 

    ![Средство просмотра журналов Функций на портале Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Теперь вы можете изменить расписание функции, чтобы она выполнялась реже, например раз в час. 

## <a name="update-the-timer-schedule"></a>Обновление расписания таймера

1. Разверните вашу функцию и щелкните **Интеграция**. Здесь вы определяете входные и выходные привязки для вашей функции, а также задаете расписание. 

2. Введите в поле **Расписания** новое значение `0 0 */1 * * *`, а затем щелкните **Сохранить**.  

![Обновление расписания таймера функций на портале Azure](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Теперь функция будет выполняться раз в час. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия
Вы создали функцию, которая выполняется на основе расписания. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о триггерах см.в статье [Настройка триггеров для выполнения кода с помощью Функций Azure](functions-bindings-timer.md). 




