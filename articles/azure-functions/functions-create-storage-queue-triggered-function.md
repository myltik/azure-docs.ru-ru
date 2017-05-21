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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b3910ba2f95bb6f3228926a51f71f96ccd1e23
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Создание функции, активируемой хранилищем очередей Azure

Узнайте, как создавать функцию, активируемую при отправке сообщений в очередь службы хранилища Azure.  

![Просмотр сообщения в журналах](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

Выполнение всех шагов в этой статье займет меньше пяти минут.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Вам необходимо скачать и установить [обозреватель службы хранилища Microsoft Azure](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Создание функции, активируемой очередью

Разверните свое приложение-функцию, нажмите кнопку **+** рядом с **Функции**, а затем щелкните шаблон **QueueTrigger** для нужного языка. Затем используйте настройки, указанные в таблице, и щелкните **Создать**.

![Создайте функцию, активируемую очередью службы хранилища.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
| Настройка      |  Рекомендуемое значение   | Описание                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **Имя очереди**   | myqueue-items    | Имя очереди для подключения к вашей учетной записи хранения. |
| **Подключение к учетной записи хранения** | AzureWebJobStorage | Вы можете использовать подключение к учетной записи хранения, которое уже используется вашим приложением-функцией, или создать его.  |
| **Имя функции** | Уникальное для вашего приложения-функции | Имя функции, активируемой очередью. |  

Затем необходимо подключиться к своей учетной записи хранения Azure и создать очередь службы хранилища **myqueue-items**.

## <a name="create-the-queue"></a>Создание очереди

1. Щелкните **Интегрировать** в своей функции, затем разверните узел **Документация** и скопируйте **имя учетной записи** и **ключ учетной записи**. Эти учетные данные используются для подключения к учетной записи хранения. Если вы уже подключились к учетной записи хранения, перейдите к шагу 4.
 
    ![Получение учетных данных для подключения к учетной записи хранения.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

2. Запустите инструмент [Обозреватель службы хранилища Microsoft Azure](http://storageexplorer.com/), щелкните значок подключения слева, выберите **Use a storage account name and key** (Использовать имя и ключ учетной записи хранения), а затем щелкните **Далее**.

    ![Запуск инструмента "Обозреватель учетной записи хранения"](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)
    
3. Введите **имя учетной записи** и **ключ учетной записи** из шага 1, щелкните **Далее**, а затем — **Подключить**. 
  
    ![Введите учетные данные хранилища и подключитесь.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

4. Разверните подключенную учетную запись хранения, щелкните правой кнопкой мыши **Очереди** (Контейнеры больших двоичных объектов), а затем щелкните **Создать очередь**, введите `myqueue-items` и нажмите клавишу ВВОД.
 
    ![Создание очереди службы хранилища](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Теперь, когда вы создали очередь хранилища, испытайте функцию, добавив сообщение в очередь.  

## <a name="test-the-function"></a>Проверка функции

1. На портале Azure перейдите к вашей функции, в нижней части страницы разверните **Журналы** и убедитесь, что потоковая передача журналов не остановлена.

2. Разверните свою учетную запись в обозревателе хранилищ, узел **Очереди** и **myqueue-items**, а затем щелкните **Add message** (Добавить сообщение). 

    ![Добавление сообщения в очередь](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

2. Введите сообщение Hello World в поле **Текст сообщения** и щелкните **ОК**.
 
3. Подождите несколько секунд, а затем вернитесь в журналы функции и убедитесь, что новое сообщение было считано из очереди. 

    ![Просмотр сообщения в журналах](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

4. Вернитесь в обозреватель хранилища, щелкните **Обновить** и убедитесь, что сообщение было обработано и больше не находится в очереди.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вы создали функцию, которая выполняется при добавлении сообщения в очередь хранилища. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о триггерах хранилища очередей см. в статье [Привязки очередей службы хранилища для Функций Azure](functions-bindings-storage-queue.md). 




