---
title: 'Учебник по службам Azure Analysis Services: занятие 7 "Создание ключевых показателей эффективности" | Документы Майкрософт'
description: Описывает создание ключевых показателей эффективности в учебном проекте служб Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8534640822030a7aa93c8eafe2a1b1a4a8bc5bc4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-key-performance-indicators"></a>Создание ключевых показателей эффективности

В этом занятии вы создадите ключевые показатели эффективности (КПЭ). Ключевые показатели эффективности используются для оценки эффективности значения, определенного *базовой* мерой, по отношению к *целевому* значению, также определенному мерой или абсолютным значением. В клиентских приложениях по ведению отчетности ключевые показатели эффективности позволяют специалистам быстро и просто получить сводку по успешности бизнеса или выявить тенденции. Дополнительные сведения см. в разделе [Ключевые показатели эффективности](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular).
  
Предполагаемое время выполнения этого занятия: **15 минут**  
  
## <a name="prerequisites"></a>предварительным требованиям  
Этот раздел входит в учебник по табличному моделированию, который следует изучать в предложенном порядке. Прежде чем выполнять задачи в этом разделе, нужно завершить предыдущее занятие: [Занятие 6. Создание мер](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Создание ключевых показателей эффективности  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>Создание ключевого показателя эффективности InternetCurrentQuarterSalesPerformance  
  
1.  В конструкторе моделей выберите таблицу **FactInternetSales**.  
  
2.  Щелкните пустую ячейку в сетке мер.  
  
3.  В строке формул над таблицей введите следующую формулу: 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Эта мера будет использоваться в качестве базовой для ключевого показателя эффективности.  
  
4.  В сетке мер щелкните правой кнопкой мыши **InternetCurrentQuarterSalesPerformance** > **Создать ключевые показатели эффективности**.   
  
5.  В поле **Цель** диалогового окна "Ключевой показатель эффективности" выберите **Абсолютное значение**, а затем введите **1.1**.  
  
7.  В левом (нижнем) поле с ползунком введите **1**, а в правом (верхнем) — **1.07**.  
  
8.  В области **Выберите стиль значка** выберите значок в виде ромба (красного цвета), треугольника (желтого цвета), круга (зеленого цвета).
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Обратите внимание на развертываемую метку **Описания** под доступными стилями значков. Используйте описания для разных элементов ключевых показателей эффективности, чтобы сделать их более понятными в клиентских приложениях.  
  
9. Нажмите кнопку **ОК**, чтобы завершить создание КПЭ.  
  
    В сетке мер обратите внимание на значок рядом с мерой **InternetCurrentQuarterSalesPerformance**. Он указывает, что мера служит базовым значением для КПЭ.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>Создание ключевого показателя эффективности InternetCurrentQuarterMarginPerformance  
  
1.  Щелкните пустую ячейку в сетке мер для таблицы **FactInternetSales**.  
  
2.  В строке формул над таблицей введите следующую формулу:  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Щелкните правой кнопкой мыши **InternetCurrentQuarterMarginPerformance** > **Создать ключевой показатель эффективности**.  
  
4.  В поле **Цель** диалогового окна "Ключевой показатель эффективности" выберите **Абсолютное значение**, а затем введите **1.25**.   
  
5.  В левом (нижнем) поле с ползунком прокрутите до значения **0.8**, а в правом (верхнем) — до значения **1.03**.  
  
6.  В области **Выберите стиль значка** выберите значок в виде ромба (красного цвета), треугольника (желтого цвета), круга (зеленого цвета) и нажмите кнопку **ОК**.  
  
## <a name="whats-next"></a>Что дальше?
[Занятие 8. Создание перспектив](../tutorials/aas-lesson-8-create-perspectives.md).
  
  
