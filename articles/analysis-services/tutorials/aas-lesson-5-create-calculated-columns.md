---
title: 'Учебник по службам Azure Analysis Services: занятие 5 "Создание вычисляемых столбцов" | Документы Майкрософт'
description: Описывает создание вычисляемых столбцов в учебном проекте служб Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 39e1da6a855ad88a02550a6c98b0fac61f3701d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596708"
---
# <a name="create-calculated-columns"></a>Создание вычисляемых столбцов

В этом занятии вы создадите в модели данные, добавив вычисляемые столбцы. Вычисляемые столбцы можно создать (в качестве настраиваемых столбцов) при использовании функции получения данных, с помощью редактора запросов или позднее в конструкторе моделей. Последним способом вы и воспользуетесь. Дополнительные сведения см. в разделе [Вычисляемые столбцы](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns).
  
Вы создадите пять вычисляемых столбцов в трех разных таблицах. Шаги для каждой задачи немного отличаются, что позволяет продемонстрировать несколько способов создания и переименования столбцов, а также их перемещения в таблице.  

Кроме того, на этом занятии вы впервые воспользуетесь выражениями анализа данных (DAX). DAX — это специальный язык, позволяющий создавать сложные настраиваемые выражения формул для табличных моделей. В этом руководстве вы будете использовать DAX для создания вычисляемых столбцов, мер и фильтров ролей. Дополнительные сведения см. в разделе [DAX в табличных моделях](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular). 
  
Предполагаемое время выполнения этого занятия: **15 минут**  
  
## <a name="prerequisites"></a>предварительным требованиям  
Этот раздел входит в учебник по табличному моделированию, который следует изучать в предложенном порядке. Прежде чем выполнять задачи в этом разделе, нужно завершить предыдущее занятие: [Занятие 4. Создание связей](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Создание вычисляемых столбцов  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Создание вычисляемого столбца MonthCalendar в таблице DimDate  
  
1.  Щелкните меню **Модель** и выберите **Представление модели** > **Представление данных**.  
  
    Вычисляемые столбцы можно создать только с помощью конструктора моделей в представлении данных.  
  
2.  В конструкторе моделей щелкните таблицу **DimDate** (вкладка).  
  
3.  Щелкните правой кнопкой мыши заголовок столбца **CalendarQuarter** и выберите пункт **Вставить столбец**.  
  
    Новый столбец с именем **Вычисляемый столбец 1** вставляется слева от столбца **Календарный квартал**.  
  
4.  В строке формул над таблицей введите приведенную ниже формулу DAX. Компонент автозаполнения помогает вводить полные имена столбцов и таблиц и выводит список доступных функций.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    После этого все строки в вычисляемом столбце заполняются значениями. Если прокрутить содержимое таблицы вниз, вы увидите, что строки могут содержать разные значения для этого столбца, в зависимости от данных в каждой строке.    
  
5.  Переименуйте этот столбец в **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
Вычисляемый столбец MonthCalendar содержит поддерживающее сортировку имя для месяца.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Создание вычисляемого столбца DayOfWeek в таблице DimDate  
  
1.  Пока таблица **DimDate** активна, откройте меню **Столбец** и выберите **Добавить столбец**.  
  
2.  В строке формул введите следующую формулу:  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Завершив составление формулы, нажмите клавишу ВВОД. С правого края таблицы добавляется новый столбец.  
  
3.  Переименуйте его в **DayOfWeek**.  
  
4.  Щелкните заголовок столбца и перетащите его на место между столбцами **EnglishDayNameOfWeek** и **DayNumberOfMonth**.  
  
    > [!TIP]  
    > Перемещение столбцов в таблице облегчает навигацию.  
  
Вычисляемый столбец DayOfWeek содержит поддерживающее сортировку имя для дня недели.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Создание вычисляемого столбца ProductSubcategoryName в таблице DimProduct  
  
  
1.  Прокрутите таблицу **DimProduct** до правого края. Найдите крайний правый столбец с именем **Добавить столбец** (курсивом) и щелкните его заголовок.  
  
2.  В строке формул введите следующую формулу:  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Переименуйте столбец в **ProductSubcategoryName**.  
  
Вычисляемый столбец ProductSubcategoryName используется для создания иерархии в таблице DimProduct, которая включает данные из столбца EnglishProductSubcategoryName таблицы DimProductSubcategory. Иерархии не могут охватывать более одной таблицы. Вы создадите иерархии позднее в занятии 9.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Создание вычисляемого столбца ProductCategoryName в таблице DimProduct  
  
1.  Пока таблица **DimProduct** активна, откройте меню **Столбец** и выберите элемент **Добавить столбец**.  
  
2.  В строке формул введите следующую формулу:  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Переименуйте столбец в **ProductCategoryName**.  
  
Вычисляемый столбец ProductCategoryName используется для создания иерархии в таблице DimProduct, которая включает данные из столбца EnglishProductCategoryName таблицы DimProductCategory. Иерархии не могут охватывать более одной таблицы.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Создание вычисляемого столбца Margin в таблице FactInternetSales  
  
1.  В конструкторе моделей выберите таблицу **FactInternetSales**.  
  
2.  Создайте вычисляемый столбец между столбцами **SalesAmount** и **TaxAmt**.  
  
3.  В строке формул введите следующую формулу:  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Переименуйте столбец в **Margin**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    Вычисляемый столбец Margin используется при анализе рентабельности для каждой продажи.  
  
## <a name="whats-next"></a>Что дальше?
[Занятие 6. Создание мер](../tutorials/aas-lesson-6-create-measures.md).
  
  
  
