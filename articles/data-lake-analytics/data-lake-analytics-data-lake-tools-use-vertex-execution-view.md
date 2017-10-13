---
title: "Использование представления выполнения вершин в инструментах Data Lake для Visual Studio | Документация Майкрософт"
description: "Сведения об использовании представления выполнения вершин для изучения заданий Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
ms.openlocfilehash: b788e7bc8ded86ebd49cc0be73e5b4e1bcbeaba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Использование представления выполнения вершин в инструментах Data Lake для Visual Studio
Сведения об использовании представления выполнения вершин для изучения заданий Data Lake Analytics.

## <a name="prerequisites"></a>Предварительные требования

Вам необходимы базовые знания об использовании Средств Data Lake для Visual Studio для разработки скриптов U-SQL.  См. [Учебник. Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Открытие представления выполнения вершин
Откройте задание U-SQL в Средствах Data Lake для Visual Studio. В левом верхнем углу щелкните **Представление выполнения вершин**. Возможно, будет предложено сначала загрузить профили. На это может уйти некоторое время в зависимости от качества подключения к сети.

![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Сведения о представлении выполнения вершин
Представление выполнения вершин состоит из трех частей.

![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

В области **Селектор вершины** слева можно выбирать вершины по характеристикам (например, первые 10 прочитанных наборов данных или выбор по этапам). Одним из наиболее часто используемых фильтров является **критический путь**. **Критический путь** — это самая длинная цепочка вершин в задании U-SQL. Определение критического пути позволяет оптимизировать задания путем проверки того, какая вершина занимает больше всего времени.
  
![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

В центральной верхней области показано **состояние выполнения всех вершин**.
  
![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

В центральной нижней области приводятся сведения о каждой вершине.
* Имя процесса — имя экземпляра вершины. Оно состоит из различных частей в StageName|VertexName|VertexRunInstance. Например, вершина SV7_Split[62].v1 означает второй запущенный экземпляр (индекс (.v1) начинается с 0) вершины номер 62 в стадии SV7_Split.
* Общие данные чтения и записи — данные, прочитанные или записанные этой вершиной.
* Состояние/состояние выхода — финальное состояние завершения вершины.
* Код выхода/тип сбоя — ошибка при сбое вершины.
* Причина создания — причина создания вершины.
* Задержка ресурса/задержка процесса/задержка очереди PN — время, потраченное вершиной на ожидание ресурсов, на обработку данных и на пребывание в очереди.
* GUID процесса/создателя — идентификатор GUID для выполняемой в данный момент вершины или ее создателя.
* Версия — N-й экземпляр выполняемой вершины (по многим причинам система может запланировать новые экземпляры вершины, например для отработки отказа, вычисления избыточности и т. д.).
* Время создания версии.
* Время начала создания процесса/время постановки процесса в очередь/время начала процесса/время завершения процесса — когда процесс вершины начинает создание, когда процесс вершины начинает постановку в очередь, когда начинается определенный процесс вершины, когда завершается определенный процесс вершины.

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о том, как записывать диагностические данные в журнал, см. в статье [Доступ к журналам диагностики для Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Для просмотра сведений о заданиях см. статью [Use Job Browser and Job View for Azure Data Lake Analytics jobs](data-lake-analytics-data-lake-tools-view-jobs.md) (Использование обозревателя заданий и представления заданий для заданий Azure Data Lake Analytics).
