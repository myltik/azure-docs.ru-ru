---
title: "Создание функции, активируемой сообщениями очереди, в Azure | Документация Майкрософт"
description: "Создавайте независимые от сервера функции, активируемые сообщениями, отправленными в очередь службы хранилища Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: e6cf8797d08609f847e33f88e78fbcd3f3743a08
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Создание функции, активируемой хранилищем очередей Azure

Узнайте, как создавать функцию, активируемую при отправке сообщений в очередь службы хранилища Azure.

![Просмотр сообщения в журналах](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

Выполнение всех шагов в этой статье займет меньше пяти минут.

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением этого примера вам понадобится сделать следующее:

- Скачайте и установите [обозреватель хранилищ Microsoft Azure](http://storageexplorer.com/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Приложение-функция успешно создана.](./media/functions-create-first-azure-function/function-app-create-success.png)

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Создание функции, активируемой очередью

Разверните свое приложение-функцию, нажмите кнопку **+** рядом с **Функции**, а затем щелкните шаблон **QueueTrigger** для нужного языка. Затем используйте настройки, указанные в таблице, и щелкните **Создать**.

![Создайте функцию, активируемую очередью службы хранилища.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

| Настройка | Рекомендуемое значение | Описание |
|---|---|---|
| **Имя очереди**   | myqueue-items    | Имя очереди для подключения к вашей учетной записи хранения. |
| **Подключение к учетной записи хранения** | AzureWebJobStorage | Вы можете использовать подключение к учетной записи хранения, которое уже используется вашим приложением-функцией, или создать его.  |
| **Имя функции** | Уникальное для вашего приложения-функции | Имя функции, активируемой очередью. |

Затем необходимо подключиться к своей учетной записи хранения Azure и создать очередь службы хранилища **myqueue-items**.

## <a name="create-the-queue"></a>Создание очереди

1. Щелкните **Интегрировать** в своей функции, затем разверните узел **Документация** и скопируйте **имя учетной записи** и **ключ учетной записи**. Эти учетные данные используются для подключения к учетной записи хранения. Если вы уже подключились к учетной записи хранения, перейдите к шагу 4.

    ![Получение учетных данных для подключения к учетной записи хранения.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

1. Запустите инструмент [Обозреватель службы хранилища Microsoft Azure](http://storageexplorer.com/), щелкните значок подключения слева, выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения), а затем щелкните **Далее**.

    ![Запуск инструмента "Обозреватель учетной записи хранения"](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Введите **имя учетной записи** и **ключ учетной записи** из шага 1, щелкните **Далее**, а затем — **Подключить**.

    ![Введите учетные данные хранилища и подключитесь.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Разверните подключенную учетную запись хранения, щелкните правой кнопкой мыши **Очереди** (Контейнеры больших двоичных объектов), а затем щелкните **Создать очередь**, введите `myqueue-items` и нажмите клавишу ВВОД.

    ![Создание очереди службы хранилища](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Теперь, когда вы создали очередь хранилища, испытайте функцию, добавив сообщение в очередь.

## <a name="test-the-function"></a>Проверка функции

1. На портале Azure перейдите к вашей функции, в нижней части страницы разверните **Журналы** и убедитесь, что потоковая передача журналов не остановлена.

1. Разверните свою учетную запись в обозревателе хранилищ, узел **Очереди** и **myqueue-items**, а затем щелкните **Add message** (Добавить сообщение).

    ![Добавление сообщения в очередь](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Введите сообщение Hello World в поле **Текст сообщения** и щелкните **ОК**.

1. Подождите несколько секунд, а затем вернитесь в журналы функции и убедитесь, что новое сообщение было считано из очереди.

    ![Просмотр сообщения в журналах](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Вернитесь в обозреватель хранилища, щелкните **Обновить** и убедитесь, что сообщение было обработано и больше не находится в очереди.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вы создали функцию, которая выполняется при добавлении сообщения в очередь хранилища.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о триггерах хранилища очередей см. в статье [Привязки очередей службы хранилища для Функций Azure](functions-bindings-storage-queue.md).
