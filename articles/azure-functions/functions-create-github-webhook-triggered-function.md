---
title: Создание функции, активируемой объектом webhook GitHub, в Azure | Документация Майкрософт
description: Создавайте независимые от сервера функции, активируемые объектом webhook GitHub, с помощью Функций Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 05ad567e407a6506222acdb66ab38c4cfab76e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30282578"
---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Создание функции, активируемой объектом webhook GitHub

Узнайте, как создать функцию, активируемую с помощью запроса HTTP объекта webhook с полезными данными GitHub.

![Функция, активируемая объектом Github webhook, на портале Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>предварительным требованиям

+ Учетная запись GitHub с хотя бы одним проектом.
+ Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Приложение-функция успешно создана.](./media/functions-create-first-azure-function/function-app-create-success.png)

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Создание функции, активируемой объектом webhook GitHub

1. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом **Функции**. Если это первая функция в приложении-функции, выберите **Пользовательская функция**. Откроется полный набор шаблонов функций.

    ![Страница быстрого начала работы с функциями на портале Azure](./media/functions-create-github-webhook-triggered-function/add-first-function.png)

2. В поле поиска введите `github` и выберите нужный язык для шаблона триггера веб-перехватчика GitHub. 

     ![Выбор шаблона триггера веб-перехватчика GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger.png) 

2. Введите **имя** функции и выберите **Создать**. 

     ![Настройка функции, активируемой веб-перехватчиком GitHub, на портале Azure](./media/functions-create-github-webhook-triggered-function/functions-create-github-webhook-trigger-2.png) 

3. В новой функции щелкните **</> Получить URL-адрес функции**, а затем скопируйте и сохраните значения. Сделайте то же самое для **получения секрета GitHub**. Эти значения используются для настройки объекта webhook на сайте GitHub.

    ![Просмотр кода функции](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Затем создайте объект webhook в репозитории GitHub.

## <a name="configure-the-webhook"></a>Настройка объекта webhook

1. На сайте GitHub перейдите в свой репозиторий. Вы можете использовать любые репозитории, для которых создали ответвления. Если вам нужно создать вилку, используйте <https://github.com/Azure-Samples/functions-quickstart>.

2. Последовательно выберите **Настройки** > **Параметры** и убедитесь, что в разделе **Функции** включен параметр **Проблемы**.

   ![Включение параметра "Проблемы"](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook.png)

1. В разделе **Настройки** последовательно выберите **Веб-перехватчики** > **Добавить веб-перехватчик**.

    ![Добавление объекта webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Используйте настройки, указанные в следующей таблице, и щелкните**Добавить веб-перехватчик**:

    ![Задание URL-адреса объекта webhook и секрета](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Параметр | Рекомендуемое значение | ОПИСАНИЕ |
|---|---|---|
| **Payload URL** (URL-адрес полезных данных) | Скопированное значение | Используйте значение, возвращенное командой **< /> Получить URL-адрес функции**. |
| **Тип содержимого** | приложение/json | Функция ожидает полезных данных JSON. |
| **Секрет**   | Скопированное значение | Используйте значение, возвращенное командой **< /> Получить секрет GitHub**. |
| Триггеры событий | Let me select individual events (Я выбираю отдельные события) | Активация происходит только во время события примечания к вопросу.  |
| | Issue comment (Примечание к вопросу) |  |

Теперь объект webhook настраивается для активации функции при добавлении нового примечания к вопросу.

## <a name="test-the-function"></a>Проверка функции

1. В репозитории GitHub откройте вкладку **Issues** (Вопросы) в новом окне браузера,

1. В новом окне щелкните **New Issue** (Создать вопрос), введите название, а затем щелкните **Submit new issue** (Отправить новый вопрос).

1. В вопросе введите комментарий и нажмите кнопку **Comment**(Добавить примечание).

    ![Добавление примечания к вопросу GitHub](./media/functions-create-github-webhook-triggered-function/functions-github-webhook-add-comment.png)

1. Вернитесь на портал и просмотрите журнал. Вы должны увидеть запись трассировки с текстом нового комментария.

     ![Просмотрите текст комментария в журналах.](./media/functions-create-github-webhook-triggered-function/function-app-view-logs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы создали функцию, которая активируется при получении запроса из веб-перехватчика GitHub.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения см. в статье [Привязки HTTP и веб-перехватчика в функциях Azure](functions-bindings-http-webhook.md).
