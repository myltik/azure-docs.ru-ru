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
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356092"
---
# <a name="application-insights-overview-dashboard-preview"></a>Панель мониторинга общих сведений Application Insights (предварительная версия)

Application Insights всегда предоставляло сводную панель обзора, чтобы быстро мгновенно оценить работоспособность и производительность приложения. Новая предварительная версия панели мониторинга общих сведений предоставляет более гибкие возможности.

## <a name="how-do-i-test-out-the-new-experience"></a>Как протестировать новую возможность?

 В Application Insights в разделе _Обзор_ выберите _Please try new Overview before it becomes the default experience_ (Опробуйте новые возможности обзора, прежде чем они будут использоваться по умолчанию).

![Предварительный просмотр](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

Это приведет к запуску новой панели мониторинга общих сведений по умолчанию:

![Область панели мониторинга общих сведений](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>Повышенная производительность

Выбор диапазона времени упростили, создав интерфейс с возможностью работы одним щелчком.

![Диапазон времени](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

В целом производительность значительно увеличилась. Каждый динамически обновляемый элемент ключевого показателя эффективности по умолчанию связан с соответствующим компонентом Application Insights. Например, при выборе неудачных запросов запустится панель _Сбои_:

![Сбои](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Панель мониторинга приложений

Панель мониторинга приложения использует существующие технологии панели мониторинга в пределах Azure для предоставления полностью настраиваемого представления панели со сведениями о работоспособности и производительности приложения.

Чтобы получить доступ к панели мониторинга по умолчанию, выберите в левом верхнем углу _Панель мониторинга приложения_.

![Представление панели мониторинга](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Если это ваш первый доступ к панели мониторинга, то будет запущено представление по умолчанию:

![Представление панели мониторинга](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

При желании можно сохранить представление по умолчанию, также можно добавить и удалить элементы на панели мониторинга для оптимального соответствия потребностям вашей команды.

> [!NOTE]
> Все пользователи, имеющие доступ к ресурсу Application Insights, используют одинаковые возможности панели мониторинга приложений. Изменения, внесенные одним пользователем, влияют на представление для всех пользователей.

Чтобы вернуться к обзору возможностей просто выберите следующее:

![Кнопка "Обзор"](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>Дополнительная информация

- [Воронки](usage-funnels.md)
- [Сохранение](app-insights-usage-retention.md)
- [Средство "Маршруты пользователей"](app-insights-usage-flows.md)
- [Панели мониторинга](app-insights-dashboards.md)
