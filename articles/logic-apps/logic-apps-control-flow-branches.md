---
title: "Параллельные ветви в Azure Logic Apps | Документация Майкрософт"
description: "Создание или соединение параллельных ветвей в приложениях логики"
services: logic-apps
keywords: "ветви, параллельная обработка"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 9a836b707a576b9a938f43397ef35c00aeb476bf
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2018
---
# <a name="create-or-join-parallel-branches-in-your-logic-app"></a>Создание или соединение параллельных ветвей в приложении логики

По умолчанию действия в приложении логики выполняются последовательно. Для выполнения независимых действий одновременно можно создать [параллельные ветви](#parallel-branches), а затем [соединить эти ветви](#join-branches) в потоке позднее. 

> [!TIP] 
> Если есть триггер, который получает массив, и нужно запустить рабочий процесс для каждого элемента массива, вы можете выполнить *индивидуальную обработку* этого массива с помощью [свойства **SplitOn** триггера](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас нет ее, вы можете [зарегистрироваться для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/). 

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Добавление параллельной ветви

Для одновременного выполнения независимых действий можно добавить параллельные ветви рядом с существующим шагом. 

![Выполнение шагов в параллельном режиме](media/logic-apps-control-flow-branches/parallel.png)

Перед продолжением рабочего процесса приложение логики ожидает завершения всех ветвей.
Параллельные ветви выполняются, только если их значения свойств `runAfter` соответствуют состоянию завершенного родительского шага. Например, оба действия `branchAction1` и `branchAction2` настроены для выполнения только тогда, когда действие `parentAction` завершается с состоянием `Succeded`.

> [!NOTE]
> Прежде чем приступать, в приложении логики уже должен присутствовать шаг, на котором можно добавить параллельные ветви.

1. На <a href="https://portal.azure.com" target="_blank">портале Azure</a> откройте приложение логики в конструкторе приложений логики.

2. Наведите указатель мыши на стрелку над шагом, для которого необходимо добавить параллельные ветви.

3. Выберите знак **плюс** (**+**), **Add a parallel branch** (Добавить параллельную ветвь), а затем элемент, который требуется добавить.

   ![Добавление параллельной ветви](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   Теперь выбранный элемент отображается в параллельной ветви.

4. Добавьте нужные шаги для каждой параллельные ветви. Чтобы добавить последующее действие в параллельную ветвь, необходимо навести указатель мыши над действием, в которое его необходимо добавить. Выберите знак **плюс** (**+**) и шаг, который вы хотите добавить.

   ![Добавление последующего шага в параллельную ветвь](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Для выполнения обратного слияния ветвей [соедините параллельные ветви](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Определение параллельной ветви (JSON)

При работе в представлении кода вместо этого можно определить параллельную структуру в определении JSON приложения логики, например:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Соединение параллельных ветвей

Для слияния параллельных ветвей внизу каждой ветви необходимо добавить шаг. Этот шаг будет выполнен, когда все параллельные ветви завершат работу.

![Соединение параллельных ветвей](media/logic-apps-control-flow-branches/join.png)

1. На [портале Azure](https://portal.azure.com) найдите приложение логики и откройте его в конструкторе приложений логики. 

2. Внизу параллельных ветвей, которые нужно соединить, добавьте шаг для выполнения.

   ![Добавление шага, который соединяет параллельные ветви](media/logic-apps-control-flow-branches/join-steps.png)

   Теперь ваши параллельные ветви объединены.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Определение соединения (JSON)

При работе в представлении кода вместо этого можно определить структуру соединения в определении JSON приложения логики, например:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Оставить предложения по функциям или проголосовать за них вы можете на [сайте отзывов пользователей Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дополнительная информация

* [Conditional statements: Run steps based on a condition in logic apps](../logic-apps/logic-apps-control-flow-conditional-statement.md) (Условные операторы. Выполнение шагов на основе условия в приложениях логики)
* [Операторы switch. Выполнение различных шагов на основе различных значений в приложениях логики](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Циклы. Обработка массивов или повторение действий до выполнения условия)
* [Области. Выполнение шагов на основе состояния группы в приложениях логики](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)