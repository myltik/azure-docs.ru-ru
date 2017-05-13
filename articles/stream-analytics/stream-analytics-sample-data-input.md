---
title: "Входные данные выборки в Azure Stream Analytics | Документация Майкрософт"
description: "Оперативно обнаруживайте проблемы при устранении неполадок с заданиями Stream Analytics."
keywords: "устранение неполадок с входными данными, выборка входных данных"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8a8664cec88f2d04a3dff55df0e6ccea1ea79422
ms.contentlocale: ru-ru
ms.lasthandoff: 05/01/2017


---
# <a name="azure-stream-analytics-input-stream-sampling"></a>Выборка потока входных данных Azure Stream Analytics

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

За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

