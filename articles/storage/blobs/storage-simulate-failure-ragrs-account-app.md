---
title: "Имитация сбоя при обращении к избыточному хранилищу с доступом на чтение в Azure | Документация Майкрософт"
description: "Имитация ошибки при обращении к геоизбыточному хранилищу с доступом на чтение"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 2919eb0e301000b53f4f63799e9c65aad45ca9f2
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Имитация сбоя при обращении к избыточному хранилищу с доступом на чтение

Это руководство представляет собой вторую часть цикла. В этом руководстве вы с помощью Fiddler создадите сообщение о сбое для запросов к учетной записи [геоизбыточного хранилища с доступом на чтение](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS), чтобы имитировать сбой и проверить переключение приложения на вторичную конечную точку.

![Приложение для сценария](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Из второй части цикла вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * запуск и приостановка приложения;
> * имитация сбоя;
> * имитация восстановления основной конечной точки.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* скачайте и установите [Fiddler](https://www.telerik.com/download/fiddler).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Чтобы выполнить инструкции этого руководства, сначала следует ознакомиться с предыдущим руководством по хранилищу: [Обеспечение высокой доступности данных приложений в хранилище Azure][previous-tutorial].

## <a name="launch-fiddler"></a>Запуск Fiddler

Откройте Fiddler, выберите **Правила** и **Настроить правила**.

![Настройка правил Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Откроется средство Fiddler ScriptEditor, в котором отображается файл **SampleRules.js**. Этот файл используется для настройки Fiddler. Скопируйте приведенный ниже пример кода и вставьте его в функцию `OnBeforeResponse`. Новый код закомментирован, чтобы реализованная в нем логика не применялась немедленно. Внеся изменения, сохраните их с помощью меню **Файл** > **Сохранить**.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Вставка настраиваемого правила](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>Запуск и приостановка приложения

В Visual Studio нажмите клавишу **F5** или щелкните **Запустить**, чтобы начать отладку приложения. Когда приложение начнет чтение из основной конечной точки, нажмите **любую клавишу** в окне консоли, чтобы приостановить работу приложения.

## <a name="simulate-failure"></a>Имитация сбоя

Теперь, когда приложение приостановлено, раскомментируйте настраиваемое правило, которые вы сохранили в Fiddler на предыдущем шаге. Этот пример кода отслеживает запросы к учетной записи хранения RA-GRS, и если путь содержит имя образа `HelloWorld`, возвращает код ответа `503 - Service Unavailable`.

Перейдите к Fiddler и выберите **Правила** -> **Настроить правила**.  Раскомментируйте приведенные ниже строки и замените `STORAGEACCOUNTNAME` именем вашей учетной записи хранения. Выберите **Файл** -> **Сохранить**, чтобы сохранить изменения.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Возобновите работу приложения, снова нажав **любую клавишу**.

Когда приложение продолжит работу, все запросы к основной конечной точке будут завершаться сбоем. Приложение выполнит пять попыток подключиться к основной конечной точке. Достигнув порогового значения (пять попыток), приложение запросит образ из вторичной конечной точки с доступом на чтение. После 20 успешных обращений для получения образа из вторичной конечной точки приложение снова попытается подключиться к основной конечной точке. Если основная конечная точка по-прежнему недоступна, приложение продолжит чтение из вторичной конечной точки. Этот алгоритм [прерывателя цепи](/azure/architecture/patterns/circuit-breaker.md) мы описывали в предыдущем руководстве.

![Вставка настраиваемого правила](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Имитация восстановления основной конечной точки

Пока в Fiddler настроен представленный выше набор правил, все запросы к основной конечной точке завершаются сбоем. Чтобы имитировать восстановление работоспособности основной конечной точки, удалите логику внедрения ошибки `503`.

Приостановите работу приложения, нажав **любую клавишу**.

### <a name="remove-the-custom-rule"></a>Удаление настраиваемого правила

Перейдите к Fiddler и выберите **Правила** > **Настроить правила**.  Закомментируйте или удалите настраиваемую логику в функции `OnBeforeResponse`, восстановив стандартный вид функции. Выберите **Файл** и **Сохранить**, чтобы сохранить изменения.

![Удаление настраиваемого правила](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Завершив этот процесс, возобновите работу приложения, нажав **любую клавишу**. Приложение продолжит чтение из основной конечной точки, пока не выполнит 999 операций чтения.

![Возобновление работы приложения](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>Дальнейшие действия

Во второй части серии вы научились имитировать сбой для проверки алгоритма обращения к геоизбыточному хранилищу с доступом на чтение, в том числе выполнили следующие операции:

> [!div class="checklist"]
> * запуск и приостановка приложения;
> * имитация сбоя;
> * имитация восстановления основной конечной точки.

Перейдите по ссылке, чтобы просмотреть готовые хранилища.

> [!div class="nextstepaction"]
> [Примеры Azure CLI для хранилища BLOB-объектов](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md