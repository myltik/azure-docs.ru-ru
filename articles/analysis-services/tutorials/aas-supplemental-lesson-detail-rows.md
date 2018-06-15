---
title: 'Дополнительное занятие для учебника по службам Azure Analysis Services: "Строки детализации" | Документы Майкрософт'
description: Описывает, как создать выражение строк детализации в учебном проекте служб Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6e15788992e66fdb8e742b290e5bcf5bce8d20fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596106"
---
# <a name="supplemental-lesson---detail-rows"></a>Дополнительное занятие. Строки детализации

В этом дополнительном занятии вы воспользуетесь редактором DAX для определения пользовательского выражения строк детализации. Выражение строк детализации — это свойство меры, предоставляющее конечным пользователям дополнительные сведения об агрегированных результатах для меры. 
  
Предполагаемое время выполнения этого занятия: **10 минут**  
  
## <a name="prerequisites"></a>предварительным требованиям  
Это дополнительное занятие входит в руководство по табличному моделированию. Прежде чем выполнять задачи из этого дополнительного занятия, следует завершить все предыдущие занятия или располагать готовым учебным проектом модели интернет-продаж Adventure Works.  
  
## <a name="whats-the-issue"></a>В чем заключается проблема?
Прежде чем добавлять выражение строк детализации, давайте взглянем на сведения о мере InternetTotalSales.

1.  В SSDT откройте меню **Модель** и выберите **Анализ в Excel**, чтобы открыть Excel и создать пустую сводную таблицу.
  
2.  В области **Поля сводной таблицы** добавьте меру **InternetTotalSales** из таблицы FactInternetSales поле **Значения**, **CalendarYear** из таблицы DimDate поле **Столбцы** и **EnglishCountryRegionName** в поле **Строки**. Теперь сводная таблица предоставляет агрегированные результаты из меры InternetTotalSales по регионам и годам. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. В сводной таблице дважды щелкните агрегированное значение для года и имени региона. Здесь мы выбрали значение для Австралии за 2014 год. Открывается новый лист с данными, но это не те данные, которые нам требуются.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Здесь нам бы хотелось увидеть таблицу со столбцами и строками данных, имеющих отношение к агрегированному результату нашей меры InternetTotalSales. Для этого можно добавить выражение строк детализации в качестве свойства меры.

## <a name="add-a-detail-rows-expression"></a>Добавление выражения строк детализации

#### <a name="to-create-a-detail-rows-expression"></a>Чтобы добавить выражение строк детализации, сделайте следующее: 
  
1. В сетке мер таблицы FactInternetSales выберите меру **InternetTotalSales**. 

2. В области **Свойства** > **Выражение строк детализации** нажмите кнопку редактора, чтобы открыть редактор DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. В редакторе DAX введите следующее выражение:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Это выражение указывает имена, столбцы и результаты мер из таблицы FactInternetSales. Когда пользователь дважды щелкает агрегированный результат в сводной таблице или отчете, открываются связанные таблицы.

4. Вернитесь в Excel, удалите лист, созданный в шаге 3, а затем дважды щелкните агрегированное значение. На этот раз с помощью определенного для меры свойства "Выражение строк детализации" открывается новый лист, содержащий гораздо больше полезных данных.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Разверните модель заново.

  
## <a name="see-also"></a>См. также  

[Функция SELECTCOLUMNS (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Дополнительное занятие. Динамическая безопасность](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Дополнительное занятие. Неоднородные иерархии](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 