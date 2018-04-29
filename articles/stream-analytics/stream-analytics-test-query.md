---
title: Проверка задания Azure Stream Analytics с примером данных | Документация Майкрософт
description: Сведения о том, как проверить запросы в заданиях Stream Analytics.
keywords: test a job, input sampling, upload sample date
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Проверка запроса Stream Analytics с примером данных

С помощью Azure Stream Analytics можно отправлять примеры данных и проверять выполнение запросов на портале без запуска или остановки заданий.

## <a name="upload-sample-data-and-test-the-query"></a>Отправка примера данных и проверка запроса

1. Перейдите к одному из существующих заданий Stream Analytics. Щелкните **Запрос**, чтобы открыть окно редактора запросов. 

2. Чтобы проверить запрос с примером входных данных, щелкните любые из них правой кнопкой мыши и выберите **Отправить образец данных из файла**. Сейчас можно отправлять только данные в формате JSON. Если данные представлены в другом формате, например CSV, перед отправкой преобразуйте их в формат JSON. Для этого можно использовать любой конвертер с отрытым кодом, например [конвертер CSV в JSON](http://www.convertcsv.com/csv-to-json.htm).

    ![Проверка запроса редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. После завершения загрузки нажмите кнопку **Тест** для проверки этого запроса на основе указанных демонстрационных данных.

    ![Проверка демонстрационных данных редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Если вы хотите сохранить выходные данные проверки для последующего использования, в браузере отображается ссылка для скачивания результатов по выходным данным запроса. Теперь можно легко и последовательно изменить запрос, а также несколько раз проверить его, чтобы увидеть, как изменятся выходные данные.

   ![Пример выходных данных редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

При использовании нескольких наборов выходных данных в запросе можно просмотреть результаты для каждого из них отдельно и легко переключаться между ними. После проверки результатов в браузере можно сохранить запрос, запустить задание и наблюдать за обработкой событий без ошибок.
За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
