---
title: "Тестирование запросов Azure Stream Analytics | Документация Майкрософт"
description: "Сведения о том, как проверить запросы в заданиях Stream Analytics."
keywords: "проверка запроса, устранение неполадок запроса"
documentation center: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5e7bab0b0c3222ba093a93dc2d15f1e41898e62c
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Проверка запросов Azure Stream Analytics на портале Azure

В Azure Stream Analytics можно проверить запросы на портале Azure, не запуская и не останавливая задание.

## <a name="test-the-input"></a>Проверки входных данных

1. Чтобы проверить пример входных данных, щелкните любые из них правой кнопкой мыши и выберите **Отправить образец данных из файла**.

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

