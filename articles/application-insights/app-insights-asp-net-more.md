---
title: Максимальная польза от Azure Application Insights | Документация Майкрософт
description: Начав работу с Application Insights, ознакомьтесь с этим списком доступных функций.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 2f03083367de4e818bdc953ab76c28ff687f0a48
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294342"
---
# <a name="more-telemetry-from-application-insights"></a>Дополнительные данные телеметрии из Application Insights
После [добавления Application Insights в код ASP.NET](app-insights-asp-net.md)можно сделать еще кое-что, чтобы получать дополнительные данные телеметрии. 

| Действие | Что вы получаете|
|---|---|
|(Серверы IIS) [Установите монитор состояния](http://go.microsoft.com/fwlink/?LinkId=506648) на каждом компьютере-сервере.<br/>(Веб-приложения Azure) На панели управления Azure веб-приложения откройте колонку Application Insights.| [**Счетчики производительности**](app-insights-performance-counters.md).<br/>[**Исключения**](app-insights-asp-net-exceptions.md) — подробные трассировки стека.<br/>[**Зависимости**](app-insights-asp-net-dependencies.md).|
|[Добавьте фрагмент JavaScript в свои веб-страницы](app-insights-javascript.md)|[Производительность страниц](app-insights-web-track-usage.md), исключения браузера, производительность вызовов AJAX. Пользовательская телеметрия на стороне клиента.|
|[Создайте веб-тесты на доступность](app-insights-monitor-web-app-availability.md)|Получение оповещений, когда сайт становится недоступным|
|Убедитесь, что MSBuild создает [файл BuildInfo.config](https://msdn.microsoft.com/library/dn449058.aspx)|[Создание заметок к диаграммам метрик](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Напишите собственные события и систему телеметрии](app-insights-api-custom-events-metrics.md)|Учет бизнес-событий и метрик, отслеживание подробных сведений об использовании и многое другое|
|[Выполните профилирование активного сайта](https://aka.ms/AIProfilerPreview)|Подробные временные показатели функций из активного веб-приложения|






