---
title: "Учебник по службам Azure Analysis Services: занятие 2 &quot;Получение данных&quot; | Документы Майкрософт"
description: "Описывает получение и импорт данных в учебном проекте служб Azure Analysis Services."
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
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 492bfd19c2b364089e13f7ae53010cb5ce14123d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/05/2017

---

# <a name="lesson-2-get-data"></a>Занятие 2. Получение данных

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

В этом занятии вы используете функцию получения данных в SSDT, чтобы подключиться к примеру базы данных AdventureWorksDW2014, выбрать данные, просмотреть и отфильтровать их, а затем импортировать в рабочую область модели.  
  
С помощью функции получения данных можно импортировать данные из самых разнообразных источников: базы данных SQL Azure, Oracle, Sybase, канала OData, Teradata, файлов и других. Данные также можно запросить с помощью выражения формулы Power Query M.
  
Предполагаемое время выполнения этого занятия: **10 минут**  
  
## <a name="prerequisites"></a>Предварительные требования  
Этот раздел входит в учебник по табличному моделированию, который следует изучать в предложенном порядке. Прежде чем выполнять задачи в этом разделе, нужно завершить предыдущее занятие: [Занятие1. Создание проекта табличной модели](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Создание подключения  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Чтобы создать подключение к базе данных AdventureWorksDW2014, сделайте следующее:  
  
1.  В обозревателе табличных моделей щелкните правой кнопкой мыши **Источники данных** > **Импорт из источника данных**.  
  
    При этом запускается функция получения данных, помогающая пользователю подключиться к источнику данных. Если вы не видите обозреватель табличных моделей в **обозревателе решений**, дважды щелкните **Model.bim** для открытия модели в конструкторе. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  В области функции получения данных щелкните **База данных** > **База данных SQL Server** > **Подключить**.  
  
3.  В поле **Сервер** диалогового окна **База данных SQL Server** введите имя сервера, где установлена база данных AdventureWorksDW2014, и нажмите кнопку **Подключить**.  

4.  При появлении запроса на ввод учетных данных нужно указать учетные данные, используемые службами Analysis Services для подключения к источнику данных при импорте и обработке данных. В области **Режим олицетворения** выберите **Олицетворить учетную запись**, а затем введите учетные данные и нажмите кнопку **Подключить**. Рекомендуется использовать учетную запись, в которой срок действия пароля не ограничен.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Использование учетной записи пользователя Windows и пароля обеспечивает наиболее безопасный способ подключения к источнику данных.
  
5.  В навигаторе выберите базу данных **AdventureWorksDW2014**, а затем нажмите кнопку **ОК**. Создается подключение к базе данных. 
  
6.  В навигаторе установите флажок для следующих таблиц: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** и **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
После нажатия кнопки "ОК" открывается редактор запросов, где в следующем разделе вы будете фильтровать импортируемые данные.

  
## <a name="filter-the-table-data"></a>Фильтрация данных таблицы  
Таблицы в примере базы данных AdventureWorksDW2014 содержат данные, которые не обязательно включать в модель. По возможности нужно отфильтровать неиспользуемые данные для экономии места в памяти, занимаемого моделью. Далее вы отфильтруете некоторые столбцы из таблиц, чтобы они не импортировались в базу данных рабочей области или базу данных модели после развертывания. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Чтобы отфильтровать данные таблицы перед импортом, сделайте следующее:  
  
1.  В редакторе запросов выберите таблицу **DimCustomer**. Открывается представление таблицы DimCustomer в источнике данных (пример базы данных AdventureWorksDWQ2014). 
  
2.  Выполните множественный выбор (CTRL+щелчок мышью) элементов **SpanishEducation**, **FrenchEducation**, **SpanishOccupation**, **FrenchOccupation**, а затем щелкните правой кнопкой мыши и выберите пункт **Удалить столбцы**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Так как значения для этих столбцов не актуальны для анализа интернет-продаж, импортировать их не требуется. После удаления ненужных столбцов модель становится компактнее и эффективнее.  
  
4.  Отфильтруйте оставшиеся таблицы, удалив из них следующие столбцы:  
    
    **DimDate**
    
      |столбец|  
      |--------|  
      |DateKey|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |столбец|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |столбец|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |столбец|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |столбец|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      |столбец|  
      |------------------|  
      |**OrderDateKey**|  
      |**DueDateKey**|  
      |**ShipDateKey**|   
  
## <a name="Import"></a>Импорт данных из выбранных таблиц и столбцов  
Теперь, когда вы просмотрели и отфильтровали ненужные данные, можно импортировать оставшиеся полезные данные. Мастер импортирует данные таблицы вместе со всеми связями между таблицами. Новые таблицы и столбцы создаются в модели, а отфильтрованные данные не импортируются.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Чтобы импортировать данные из выбранных таблиц и столбцов, сделайте следующее:  
  
1.  Просмотрите выбранные параметры. Если все настроено правильно, нажмите кнопку **Импорт**. В диалоговом окне "Обработка данных" отображается состояние данных, импортируемых из источника данных в базу данных рабочей области.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Нажмите кнопку **Закрыть**  

  
## <a name="save-your-model-project"></a>Сохранение проекта модели  
Важно регулярно сохранять проект модели.  
  
#### <a name="to-save-the-model-project"></a>Чтобы сохранить проект модели, сделайте следующее:  
  
-   Щелкните **Файл** > **Сохранить все**.  
  
## <a name="whats-next"></a>Что дальше?
[Занятие 3. Обозначение таблицы дат](../tutorials/aas-lesson-3-mark-as-date-table.md).

  
  

