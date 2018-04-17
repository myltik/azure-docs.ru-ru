---
title: Тестирование запросов Azure Stream Analytics | Документация Майкрософт
description: Сведения о том, как проверить запросы в заданиях Stream Analytics.
keywords: проверка запроса, устранение неполадок запроса
documentation center: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 50bfce426bf48ba986887f8a2e2873fe04ea2507
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Проверка запросов Azure Stream Analytics на портале Azure

В Azure Stream Analytics можно проверить запросы на портале Azure, не запуская и не останавливая задание.

## <a name="test-the-input"></a>Проверки входных данных

1. Чтобы проверить пример входных данных, щелкните любые из них правой кнопкой мыши и выберите **Отправить образец данных из файла**. Сейчас можно отправлять только данные в формате JSON. Если данные представлены в другом формате, например CSV, перед отправкой преобразуйте их в формат JSON. Для этого можно использовать любой конвертер с отрытым кодом, например [конвертер CSV в JSON](http://www.convertcsv.com/csv-to-json.htm).

    ![Проверка запроса редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. После завершения загрузки нажмите кнопку **Тест** для проверки этого запроса на основе указанных демонстрационных данных.

    ![Проверка демонстрационных данных редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

Если вам понадобится сохранить выходные данные проверки для последующего использования, в браузере отображается ссылка для скачивания результатов по выходным данным запроса. Теперь можно легко и последовательно изменить запрос, а также несколько раз проверить его, чтобы увидеть, как изменятся выходные данные.

![Пример выходных данных редактора запросов Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

При использовании нескольких наборов выходных данных в запросе можно просмотреть результаты для каждого из них отдельно и легко переключаться между ними.

Если результаты в браузере удовлетворительны, можно сохранить запрос, запустить задание и наблюдать за обработкой событий без ошибок.

## <a name="get-help"></a>Получение справки

За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
