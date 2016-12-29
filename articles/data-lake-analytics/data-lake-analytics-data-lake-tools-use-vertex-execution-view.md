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
translationtype: Human Translation
ms.sourcegitcommit: ac5a64b759376c06e058ae015b73f1b73b7d1e7b
ms.openlocfilehash: b2acf4fd95c1611ecd580b508339d1305010fdd8


---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Использование представления выполнения вершин в инструментах Data Lake для Visual Studio
Сведения об использовании представления выполнения вершин для изучения заданий Data Lake Analytics.

## <a name="prerequisites"></a>Предварительные требования
* Базовые знания об использовании инструментов Data Lake для Visual Studio для разработки скриптов U-SQL.  См. [Учебник. Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Открытие представления выполнения вершин
Для выполнения конкретного задания можно щелкнуть ссылку Vertex Execution View (Представление выполнения вершин) в левом нижнем углу. Возможно, будет предложено сначала загрузить профили. На это может уйти некоторое время в зависимости от качества подключения к сети.

![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Сведения о представлении выполнения вершин
Представление выполнения вершин состоит из трех частей.

![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

* Слева находится выбор вершин.  Вершины можно выбирать по характеристикам (например, первые 10 прочитанных наборов данных или выбор по этапам).
  
    Одним из наиболее распространенных фильтров является Critical path (Критический путь). Критический путь — это самый длинный путь задания U-SQL. Он используется для оптимизации заданий и позволяет проверить, какая вершина занимает больше всего времени.
* Область сверху по центру:
  
    ![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)
  
    Это представление также показывает состояние выполнения всех вершин. Оно преобразует время в соответствии с настройками локального компьютера и показывает различные состояния разными цветами.
* Область снизу по центру:
  
    ![Представление выполнения вершин средств Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)
  
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
* Общие сведения об Azure Data Lake Analytics см. в [этой статье](data-lake-analytics-overview.md).
* Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
* Сведения о том, как записывать диагностические данные в журнал, см. в статье [Доступ к журналам диагностики для Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Для просмотра сведений о заданиях см. статью [Use Job Browser and Job View for Azure Data Lake Analytics jobs](data-lake-analytics-data-lake-tools-view-jobs.md) (Использование обозревателя заданий и представления заданий для заданий Azure Data Lake Analytics).
* Подробные сведения об инструментах Data Lake Tools для Visual Studio Code см. в статье [Use the Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md) (Использование Azure Data Lake Tools для Visual Studio Code).



<!--HONumber=Nov16_HO4-->


