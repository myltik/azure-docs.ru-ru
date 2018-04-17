---
title: Проверка запроса с помощью примера данных в Azure Stream Analytics
description: Из этой статьи вы узнаете, как проверить запрос с помощью нескольких примеров входных данных в Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/12/2018
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Проверка запроса и выборка входных данных в Azure Stream Analytics 

Используя Azure Stream Analytics, можно создать выборку входных событий из файла и проверить запросы на портале без необходимости запуска или остановки задания.

## <a name="testing-your-query"></a>Тестирование запроса

На панели сведений о задании Stream Analytics откройте колонку **редактора запросов**, щелкнув имя запроса в разделе **Запрос**. (В нашем примере сценария щелкните заполнитель **< >**, так как запрос еще не создан.)

![Редактор запросов Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

Появится колонка полнофункционального редактора для создания запроса (как и в предыдущей версии). Теперь в колонке есть новая панель слева, в которой отображаются входные и выходные данные, используемые в запросе и определенные для этого задания.

![Список входных и выходных данных редактора запросов Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Кроме того, показаны и дополнительные не определенные входные и выходные данные. Они находятся в новом шаблоне запроса, с которого вы начнете свою работу. При изменении запроса они изменяются и могут даже полностью исчезнуть. Сейчас их можно игнорировать.

Чтобы проверить пример входных данных, щелкните любые из них правой кнопкой мыши и выберите **Отправить образец данных из файла**.

![Команда "Отправить образец данных из файла" редактора запросов Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

После завершения загрузки нажмите кнопку **Тест** для проверки этого запроса на основе указанных демонстрационных данных.

![Кнопка "Тест" редактора запросов Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Если вы хотите сохранить выходные данные проверки для последующего использования, в браузере отображается ссылка для скачивания результатов по выходным данным запроса. Теперь можно легко и последовательно изменить запрос, а также несколько раз проверить его, чтобы увидеть, как изменятся выходные данные.

![Пример выходных данных редактора запросов Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

На предыдущем рисунке видно, что добавлен второй набор выходных данных с именем **HighAvgTempOutput**.

При использовании нескольких наборов выходных данных в запросе можно просмотреть результаты для каждого из них отдельно и легко переключаться между ними.

Если результаты удовлетворительные, вы можете сохранить запрос, запустить задание и наблюдать за работой Stream Analytics.

## <a name="get-help"></a>Получение справки

За дополнительной помощью обращайтесь на [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дополнительная информация
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
