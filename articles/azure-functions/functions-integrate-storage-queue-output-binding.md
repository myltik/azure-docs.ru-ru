---
title: "Создание функции, активируемой сообщениями очереди, в Azure | Документация Майкрософт"
description: "Создавайте независимые от сервера функции, активируемые сообщениями, отправленными в очередь службы хранилища Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d1ddfbe9a0a0c7c7e0a060776938bd68a87e1ba5
ms.contentlocale: ru-ru
ms.lasthandoff: 06/26/2017

---
<a id="add-messages-to-an-azure-storage-queue-using-functions" class="xliff"></a>

# Добавление сообщений в очередь службы хранилища Azure с помощью Функций

В Функциях Azure входные и выходные привязки предоставляют декларативный способ подключения данных внешних служб к функции. В этой статье вы узнаете, как обновить имеющуюся функцию, добавив выходную привязку, которая отправляет сообщения в хранилище очередей Azure.  

![Просмотр сообщения в журналах](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

<a id="prerequisites" class="xliff"></a>

## Предварительные требования 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Установите [обозреватель службы хранилища Microsoft Azure](http://storageexplorer.com/).

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>Добавление выходной привязки
 
1. Разверните ваше приложение-функцию и функцию.

2. Щелкните **Интегрировать** и **+Новые выходные данные**, а затем щелкните **Хранилище очередей Azure** и **Выбрать**.
    
    ![Добавление выходной привязки хранилища очередей к функции на портале Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Используйте настройки, указанные в таблице, а затем щелкните **Сохранить**: 

    ![Добавление выходной привязки хранилища очередей к функции на портале Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Настройка      |  Рекомендуемое значение   | Описание                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя очереди**   | myqueue-items    | Имя очереди для подключения к вашей учетной записи хранения. |
    | **Подключение к учетной записи хранения** | AzureWebJobStorage | Вы можете использовать подключение к учетной записи хранения, которое уже используется вашим приложением-функцией, или создать его.  |
    | **Имя параметра сообщения** | outQueueItem | Имя параметра выходной привязки. | 

Теперь, когда выходная привязка определена, вам нужно обновить код, чтобы использовать привязку для добавления сообщений в очередь.  

<a id="update-the-function-code" class="xliff"></a>

## Обновление кода функции

1. Щелкните функцию для отображения ее кода в редакторе. 

2. Обновите определение функции C# как показано ниже, чтобы добавить параметр привязки хранилища **outQueueItem**. Пропустите этот шаг для функции JavaScript.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Добавьте следующий код к функции непосредственно перед возвращением метода. Используйте соответствующий фрагмент для языка функции.

    ```javascript
    context.bindings.outQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Щелкните **Сохранить**, чтобы сохранить изменения.

Значение, переданное триггеру HTTP, включено в сообщение, которое добавлено в очередь.
 
<a id="test-the-function" class="xliff"></a>

## Проверка функции 

1. Сохранив изменения в коде, щелкните **ОК**. 

    ![Добавление выходной привязки хранилища очередей к функции на портале Azure.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Проверьте журналы, чтобы убедиться, что функция успешно выполнена. Новая очередь с именем **outqueue** создается в вашей учетной записи хранения средой выполнения Функции при первом использовании выходной привязки.

Затем вы можете подключиться к своей учетной записи хранения, чтобы проверить новую очередь и добавленное в нее сообщение. 

<a id="connect-to-the-queue" class="xliff"></a>

## Подключение к очереди

Пропустите первые три шага, если вы уже установили обозреватель хранилищ и подключили его к своей учетной записи хранения.    

1. Щелкните **Интегрировать** в функции, затем — новую выходную привязку **Хранилище очередей Azure**, после чего разверните узел **Документация**. Скопируйте **имя учетной записи** и **ключ учетной записи**. Эти учетные данные используются для подключения к учетной записи хранения.
 
    ![Получение учетных данных для подключения к учетной записи хранения.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Запустите инструмент [Обозреватель службы хранилища Microsoft Azure](http://storageexplorer.com/), щелкните значок подключения слева, выберите **Использовать имя и ключ учетной записи хранения**, а затем щелкните **Далее**.

    ![Запуск инструмента "Обозреватель учетной записи хранения"](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Укажите **имя учетной записи** и **ключ учетной записи** из шага 1 в соответствующих полях, а затем щелкните **Далее** и **Подключить**. 
  
    ![Укажите учетные данные хранилища и подключитесь.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Разверните подключенную учетную запись хранения, щелкните правой кнопкой мыши **Очереди** и убедитесь, что очередь с именем **myqueue-items** существует. Также вы увидите сообщение, которое уже находится в очереди.  
 
    ![Создание очереди службы хранилища](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

<a id="clean-up-resources" class="xliff"></a>

## Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Вы добавили выходную привязку в имеющуюся функцию. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о привязках к хранилищу очередей см.в статье [Привязки очередей службы хранилища для Функций Azure](functions-bindings-storage-queue.md). 




