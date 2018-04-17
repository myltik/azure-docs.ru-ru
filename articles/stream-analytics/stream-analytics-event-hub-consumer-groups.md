---
title: Отладка Azure Stream Analytics с помощью приемников концентратора событий | Документация Майкрософт
description: Рассматривайте рекомендации по группам потребителей концентраторов событий в заданиях Stream Analytics.
keywords: ограничение концентратора событий, группа потребителей
services: stream-analytics
documentationcenter: ''
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
ms.openlocfilehash: 48fa5d0274549aa35e67526a758eef1f34198a6a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Отладка Azure Stream Analytics с помощью приемников концентратора событий

Вы можете использовать концентраторы событий Azure в Azure Stream Analytics для приема или вывода данных в задании. Мы рекомендуем использовать концентраторы событий с несколькими группами потребителей, чтобы обеспечить масштабируемость задания. Одной из причин является то, что число модулей чтения в задании Stream Analytics для определенных входных данных влияет на число модулей чтения в одной группе потребителей. Точное число приемников зависит от сведений о внутренней реализации логики топологии развертывания. Число приемников не предоставляется извне. Число модулей чтения можно изменить во время запуска или обновления задания.

> [!NOTE]
> При изменении числа модулей чтения во время обновления задания временные предупреждения записываются в журналы аудита. Задания Stream Analytics восстанавливаются от этих временных сбоев автоматически.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Число модулей чтения каждого раздела превышает предельно допустимое число концентраторов событий (пять)

Ниже представлены сценарии, в которых число модулей чтения каждого раздела превышает предельно допустимое число концентраторов событий (пять).

* Несколько инструкций SELECT. При использовании нескольких инструкций SELECT, ссылающихся на **один** набор входных данных концентратора событий, каждая из них создает по приемнику.
* UNION. При использовании UNION несколько наборов входных данных могут ссылаться на **один** концентратор событий и на одну группу потребителей.
* SELF JOIN. При использовании операции SELF JOIN можно создать несколько ссылок на **один** концентратор событий.

## <a name="solution"></a>Решение

Следующие рекомендации могут помочь справиться со сценариями, в которых число модулей чтения каждого раздела превышает предельно допустимое число концентраторов событий (пять).

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Разбиение запроса на несколько шагов с помощью предложения WITH

Предложение WITH задает временный именованный результирующий набор, на который можно создать ссылку в предложении FROM в запросе. Предложение WITH определяется в области выполнения одной инструкции SELECT.

Например, вместо этого запроса:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Используйте этот:

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Привязка входных данных к разным группам потребителей

Создайте отдельные группы потребителей для запросов, в которых три или более наборов входных данных подключены к одной группе потребителей концентраторов событий. Для этого нужно создать дополнительные наборы входных данных Stream Analytics.


## <a name="get-help"></a>Получение справки
Дополнительную помощь вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дополнительная информация
* [Что такое Stream Analytics?](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий Azure Stream Analytics для повышения пропускной способности базы данных](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Справочник по языку запросов Stream Analytics)
* [Stream Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx) (Справочник по API-интерфейсу REST для управления Stream Analytics)
