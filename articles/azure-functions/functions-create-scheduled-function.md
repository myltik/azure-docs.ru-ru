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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 1478a2eedad496d3113fef28920d10859d11b1ce
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Создание в Azure функции, активируемой по таймеру

Узнайте, как создать функцию, которая выполняется на основе определенного расписания с помощь Функций Azure.

![Создание приложения-функции на портале Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Приложение-функция успешно создана.](./media/functions-create-first-azure-function/function-app-create-success.png)

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Создание функции, активируемой по таймеру

1. Разверните свое приложение-функцию, нажмите кнопку **+** рядом с пунктом **Функции**, а затем щелкните шаблон **TimerTrigger** для нужного языка. Затем используйте настройки, указанные в таблице, и щелкните**Создать**:

| Настройка | Рекомендуемое значение | Описание |
|---|---|---|
| **Имя функции** | TimerTriggerCSharp1 | Определяет имя функции, активируемой по таймеру. |
| **[Расписание](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | [Выражение CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) с шестью полями, в котором запланировано ежеминутное выполнение функции. |

Будет создана функция на выбранном вами языке, которая будет выполняться каждую минуту.

1. Проверьте выполнение, просмотрев записанные в журналах сведения трассировки.

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
