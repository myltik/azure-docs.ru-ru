---
title: 'Дополнительное занятие для учебника по службам Azure Analysis Services: "Неоднородные иерархии" | Документы Майкрософт'
description: Описывает исправление неоднородных иерархий в учебном проекте служб Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 955bc57bbf5f8bc3f0d91350f885d5dc77db93a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Дополнительное занятие. Неоднородные иерархии

В этом дополнительном занятии вы устраните распространенную проблему, которая возникает при сведении иерархий, содержащих пустые значения (члены) на различных уровнях. Например, организация, где прямыми подчиненными высокопоставленного руководителя являются как руководители подразделений, так и не руководящие работники. Или географические иерархии, состоящие из комбинации страна+регион+город, где некоторые города не относятся к конкретному штату или конкретной провинции, например Вашингтон, округ Колумбия, или Ватикан. Когда иерархия содержит пустые члены, она часто опускается на другие — или неоднородные — уровни.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Табличные модели на уровне совместимости 1400 имеют дополнительное свойство **Скрыть члены** для иерархий. Значение **По умолчанию** предполагает отсутствие пустых членов на всех уровнях. Значение **Скрыть пустые члены** исключает пустые члены из иерархии при добавлении в сводную таблицу или отчет.  
  
Предполагаемое время выполнения этого занятия: **20 минут**  
  
## <a name="prerequisites"></a>предварительным требованиям  
Это дополнительное занятие входит в учебник по табличному моделированию. Прежде чем выполнять задачи из этого дополнительного занятия, следует завершить все предыдущие занятия или располагать готовым учебным проектом модели интернет-продаж Adventure Works. 

Если вы создали проект AW Internet Sales во время прохождения этого учебника, то ваша модель еще не содержит неоднородные иерархии или данные. Для выполнения этого дополнительного занятия нужно сначала создать проблему, добавив несколько дополнительных таблиц, создать связи, вычисляемые столбцы, меры и новую иерархию "Organization". Эта часть займет около 15 минут. Затем вам потребуется решить проблему за несколько минут.  

## <a name="add-tables-and-objects"></a>Добавление таблиц и объектов
  
### <a name="to-add-new-tables-to-your-model"></a>Порядок добавления таблиц в модель
  
1.  В обозревателе табличных моделей разверните пункт **Источники данных**, щелкните правой кнопкой мыши подключение и выберите элемент **Импортировать новые таблицы**.
  
2.  В навигаторе выберите **DimEmployee** и **FactResellerSales**, а затем нажмите кнопку **ОК**.

3.  В редакторе запросов щелкните элемент **Импорт**.

4.  Создайте следующие [связи](../tutorials/aas-lesson-4-create-relationships.md):

    | Таблица 1           | столбец       | Направление фильтра   | Таблица 2     | столбец      | Активна |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | значение по умолчанию            | DimDate     | Дата        | Yes    |
    | FactResellerSales | DueDate      | значение по умолчанию            | DimDate     | Дата        | Нет      |
    | FactResellerSales | ShipDateKey  | значение по умолчанию            | DimDate     | Дата        | Нет      |
    | FactResellerSales | ProductKey   | значение по умолчанию            | DimProduct  | ProductKey  | Yes    |
    | FactResellerSales | EmployeeKey  | В обе таблицы | DimEmployee | EmployeeKey | Yes    |

5. В таблице **DimEmployee** создайте следующие [вычисляемые столбцы](../tutorials/aas-lesson-5-create-calculated-columns.md): 

    **Путь** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
    ```

6.  В таблице **DimEmployee** создайте [иерархию](../tutorials/aas-lesson-9-create-hierarchies.md) с именем **Organization**. Добавьте следующие столбцы в указанном порядке: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  В таблице **FactResellerSales** создайте следующую [меру](../tutorials/aas-lesson-6-create-measures.md):

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Используйте функцию [Анализ в Excel](../tutorials/aas-lesson-12-analyze-in-excel.md), чтобы открыть Excel и автоматически создать сводную таблицу.

9.  В области **Поля сводной таблицы** добавьте иерархию **Organization** из таблицы **DimEmployee** в поле **Строки** и меру **ResellerTotalSales** из таблицы **FactResellerSales** в поле **Значения**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Как можно видеть в сводной таблице, иерархия отображает неоднородные строки. Имеется множество строк с пустыми членами.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Исправление неоднородной иерархии с помощью установки свойства "Скрыть члены"

1.  В **обозревателе табличных моделей** разверните пункт **Таблицы** > **DimEmployee** > **Иерархии** > **Organization**.

2.  В области **Свойства** > **Скрыть члены** выберите **Скрыть пустые члены**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Вернитесь в Excel и обновите сводную таблицу. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Теперь все выглядит гораздо лучше!

## <a name="see-also"></a>См. также   
[Занятие 9. Создание иерархий](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Дополнительное занятие. Динамическая безопасность](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Дополнительное занятие. Строки детализации](../tutorials/aas-supplemental-lesson-detail-rows.md)  
