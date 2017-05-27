---
title: "Создание функции, активируемой объектом webhook GitHub, в Azure | Документация Майкрософт"
description: "Создавайте независимые от сервера функции, активируемые объектом webhook GitHub, с помощью Функций Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: d79ce0e047e71d9f6af7ca55f55bea405c280b1d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/18/2017


---
# <a name="create-a-function-triggered-by-a-github-webhook"></a>Создание функции, активируемой объектом webhook GitHub

Узнайте, как создать функцию, активируемую с помощью запроса HTTP объекта webhook с полезными данными GitHub.

![Функция, активируемая объектом Github webhook, на портале Azure](./media/functions-create-github-webhook-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением этого примера вам понадобится следующее:

- Учетная запись GitHub с хотя бы одним проектом.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Приложение-функция успешно создана.](./media/functions-create-first-azure-function/function-app-create-success.png)

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-a-github-webhook-triggered-function"></a>Создание функции, активируемой объектом webhook GitHub

1. Разверните приложение-функцию, нажмите кнопку **+** рядом с пунктом **Функции**, щелкните шаблон **GitHubWebHook** для нужного языка. **Присвойте функции имя**, а затем щелкните **Создать**.

1. В новой функции щелкните **</> Получить URL-адрес функции**, а затем скопируйте и сохраните значения. Сделайте то же самое для **получения секрета GitHub**. Эти значения используются для настройки объекта webhook на сайте GitHub.

    ![Просмотр кода функции](./media/functions-create-github-webhook-triggered-function/functions-copy-function-url-github-secret.png)

Затем создайте объект webhook в репозитории GitHub.

## <a name="configure-the-webhook"></a>Настройка объекта webhook

1. На сайте GitHub перейдите в свой репозиторий. Вы можете использовать любые репозитории, для которых создали ответвления. Если потребуется разветвление репозитория, используйте <https://github.com/Azure-Samples/functions-quickstart>.

1. Щелкните **Параметры**, **Веб-перехватчики**, а затем — **Добавить веб-перехватчик**.

    ![Добавление объекта webhook GitHub](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-2.png)

1. Используйте настройки, указанные в таблице, и щелкните**Добавить веб-перехватчик**.

    ![Задание URL-адреса объекта webhook и секрета](./media/functions-create-github-webhook-triggered-function/functions-create-new-github-webhook-3.png)

| Настройка | Рекомендуемое значение | Описание |
|---|---|---|
| **Payload URL** (URL-адрес полезных данных) | Скопированное значение | Используйте значение, возвращенное командой **< /> Получить URL-адрес функции**. |
| **Секрет**   | Скопированное значение | Используйте значение, возвращенное командой **< /> Получить секрет GitHub**. |
| **Тип содержимого** | приложение/json | Функция ожидает полезных данных JSON. |
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

## <a name="next-steps"></a>Дальнейшие действия

Вы создали функцию, которая выполняется при получении запроса объекта webhook GitHub. 
[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]Дополнительные сведения см. в статье [Привязки HTTP и webhook в функциях Azure](functions-bindings-http-webhook.md).
