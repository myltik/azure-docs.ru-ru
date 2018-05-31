---
title: Панель мониторинга общих сведений об Azure Application Insights | Документация Майкрософт
description: Мониторинг приложений с помощью Azure Application Insights и функции "Панель мониторинга общих сведений".
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 89ad96441ca4d519a9f4213ae3353eea1f0a7c37
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34163642"
---
# <a name="application-insights-overview-dashboard-preview"></a>Панель мониторинга общих сведений Application Insights (предварительная версия)

Application Insights всегда предоставляло сводную панель обзора, чтобы быстро мгновенно оценить работоспособность и производительность приложения. Новая предварительная версия панели мониторинга общих сведений предоставляет более гибкие возможности.

## <a name="how-do-i-test-out-the-new-experience"></a>Как протестировать новую возможность?

 В Application Insights в разделе _Исследовать_ выберите _Обзор (предварительная версия)_.

![Предварительный просмотр](.\media\app-insights-overview-dashboard\01.png)

Это приведет к запуску новой панели мониторинга общих сведений по умолчанию:

![Область панели мониторинга общих сведений](.\media\app-insights-overview-dashboard\02.png)

## <a name="better-performance"></a>Повышенная производительность

Выбор диапазона времени упростили, создав интерфейс с возможностью работы одним щелчком.

![Диапазон времени](.\media\app-insights-overview-dashboard\04.png)

В целом производительность значительно увеличилась. Каждый динамически обновляемый элемент ключевого показателя эффективности по умолчанию связан с соответствующим компонентом Application Insights. Например, при выборе неудачных запросов запустится панель _Сбои_:

![Сбои](.\media\app-insights-overview-dashboard\03.png)

## <a name="application-dashboard"></a>Панель мониторинга приложений

Панель мониторинга приложения использует существующие технологии панели мониторинга в пределах Azure для предоставления полностью настраиваемого представления панели со сведениями о работоспособности и производительности приложения.

Чтобы получить доступ к панели мониторинга по умолчанию, выберите в левом верхнем углу _Панель мониторинга приложения_.

![Представление панели мониторинга](.\media\app-insights-overview-dashboard\0009.png)

Если это ваш первый доступ к панели мониторинга, то будет запущено представление по умолчанию:

![Представление панели мониторинга](.\media\app-insights-overview-dashboard\06.png)

При желании можно сохранить представление по умолчанию, также можно добавить и удалить элементы на панели мониторинга для оптимального соответствия потребностям вашей команды.

Чтобы вернуться к обзору возможностей просто выберите следующее:

![Кнопка "Обзор"](.\media\app-insights-overview-dashboard\07.png)
