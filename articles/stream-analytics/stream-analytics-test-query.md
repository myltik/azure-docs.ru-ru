---
title: Проверка задания Azure Stream Analytics с демонстрационными данными
description: Сведения о том, как проверить запросы в заданиях Stream Analytics.
keywords: В этой статье описывается тестирование задания Azure Stream Analytics, примера входных данных и отправка демонстрационных данных на портале Azure.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312996"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Проверка запроса Stream Analytics с демонстрационными данными

С помощью Azure Stream Analytics можно отправлять демонстрационные данные и проверять выполнение запросов на портале Azure без запуска или остановки заданий.

## <a name="upload-sample-data-and-test-the-query"></a>Отправка примера данных и проверка запроса

1. Войдите на портал Azure. 

2. Найдите имеющееся задание Stream Analytics и выберите его.

3. На странице задания Stream Analytics в разделе **Топологии задания** установите флажок **Запрос**, чтобы открыть окно редактора запросов. 

4. Чтобы проверить запрос с использованием примера входных данных, щелкните любые входные данные правой кнопкой мыши.  Затем выберите **Отправить образец данных из файла**.

   Нужно использовать только данные в формате JSON. Если данные представлены в другом формате, например CSV, перед отправкой преобразуйте их в формат JSON. Для этого можно использовать любой конвертер с отрытым кодом, например [конвертер CSV в JSON](http://www.convertcsv.com/csv-to-json.htm).

    ![Проверка запроса редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. После отправки нажмите кнопку **Тест** для проверки этого запроса на основе указанных демонстрационных данных.

    ![Проверка демонстрационных данных редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Если выходные данные проверки нужны для последующего использования, в браузере отображается ссылка для скачивания результатов по выходным данным запроса. 

7. Итеративно измените запрос и проверьте его еще раз, чтобы увидеть, каким образом изменяются выходные данные.

   ![Пример выходных данных редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   При использовании в запросе нескольких наборов выходных данных результаты отображаются на отдельных вкладках, между которыми можно легко переключаться.

8. После проверки результатов, показанных в браузере, **сохраните** запрос. Затем **запустите** задание и позвольте ему обрабатывать входящие события.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
