---
title: "Учебник по службам Azure Analysis Services: занятие 6 &quot;Создание мер&quot; | Документы Майкрософт"
description: "Описывает создание мер в учебном проекте служб Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/02/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: c4977758997c91f0191e0367fb57923f43080b56
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-6-create-measures"></a>Занятие 6. Создание мер
В этом занятии вы будете создавать меры для включения в модель. Аналогично созданным вами вычисляемым столбцам, мера представляет собой расчет, создаваемый с помощью формулы DAX. Однако в отличие от вычисляемых столбцов меры вычисляются на основе выбранного пользователем *фильтра*, например определенного столбца или среза, добавленного в поле "Метки строк" сводной таблицы. Значение для каждой ячейки в фильтре вычисляется с учетом применимой меры. Меры — это эффективные и гибкие средства вычисления, которые пригодятся почти в любых табличных моделях для динамических расчетов с числовыми данными. Дополнительные сведения см. в статье [Меры](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular).
  
Для создания мер используйте *сетку мер*. По умолчанию каждая таблица имеет пустую сетку мер. Однако меры обычно требуется создавать не для всех таблиц. Сетка мер отображается под таблицей в конструкторе моделей при использовании представления данных. Чтобы скрыть или отобразить сетку мер для таблицы, откройте меню **Таблица** и выберите **Показать сетку мер**.  
  
Можно создать меру, щелкнув пустую ячейку в сетке мер и введя формулу DAX в строке формул. После нажатия клавиши ВВОД для формулы мера появляется в ячейке. Кроме того, можно создать меры с помощью стандартной статистической функции, щелкнув столбец и нажав кнопку "Автосумма" (**∑**) на панели инструментов. Меры, созданные с помощью функции "Автосумма", появляются в ячейке сетки мер прямо под соответствующим столбцом, но их можно перемещать.  
  
В этом занятии вы создадите меры как посредством ввода формулы DAX в строке формул, так и с помощью функции "Автосумма".  
  
Предполагаемое время выполнения этого занятия: **30 минут**  
  
## <a name="prerequisites"></a>Предварительные требования  
Этот раздел входит в учебник по табличному моделированию, который следует изучать в предложенном порядке. Прежде чем выполнять задачи в этом разделе, нужно завершить предыдущее занятие: [Занятие 5. Создание вычисляемых столбцов](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Создание мер  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Создание меры DaysCurrentQuarterToDate в таблице DimDate  
  
1.  В конструкторе моделей щелкните таблицу **DimDate**.  
  
2.  В сетке мер щелкните верхнюю левую пустую ячейку.  
  
3.  В строке формул введите следующую формулу:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Обратите внимание, что верхняя левая ячейка теперь содержит имя меры **DaysCurrentQuarterToDate** и результат **92**.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    В отличие от вычисляемых столбцов для формул мер можно указать имя меры, затем запятую и затем выражение формулы.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Создание меры DaysInCurrentQuarter в таблице DimDate  
  
1.  Пока таблица **DimDate** активна в конструкторе моделей, щелкните пустую ячейку под только что созданной мерой в сетке мер.  
  
2.  В строке формул введите следующую формулу:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    При задании коэффициента сопоставления между одним неполным периодом и предыдущим периодом формула должна использовать пропорцию из истекшего периода и сравнивать ее с такой же пропорцией в предыдущем периоде. В этом случае [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] дает пропорцию для текущего периода.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Создание меры InternetDistinctCountSalesOrder в таблице FactInternetSales  
  
1.  Щелкните таблицу **FactInternetSales**.   
  
2.  Щелкните заголовок столбца **SalesOrderNumber**.  
  
3.  На панели инструментов щелкните стрелку вниз рядом с кнопкой "Автосумма" (**∑**) и выберите **DistinctCount**.  
  
    Функция "Автосумма" автоматически создает меру для выбранного столбца с помощью стандартной статистической формулы DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  Щелкните новую меру в сетке мер, а затем в поле **Имя меры** окна **Свойства** переименуйте эту меру в **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Создание дополнительных мер в таблице FactInternetSales  
  
1.  С помощью функции "Автосумма" создайте и назовите следующие меры:  

    |столбец|Имя меры|Автосумма (∑)|Формула|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Count|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Sum|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Sum|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Sum|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Sum|=SUM([SalesAmount])|  
    |Margin|InternetTotalMargin|Sum|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Sum|=SUM([TaxAmt])|  
    |Freight|InternetTotalFreight|Sum|=SUM([Freight])|  
  
2.  Создайте и назовите следующие меры в указанном порядке, щелкнув пустую ячейку в сетке мер или используя строку формул:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
Меры, созданные для таблицы FactInternetSales, можно использовать для анализа критических финансовых данных, таких как продажи, затраты и удельная прибыль, для элементов, определенных с помощью выбранного пользователем фильтра.  
  
## <a name="whats-next"></a>Что дальше?
[Занятие 7. Создание ключевых показателей эффективности](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  

  

