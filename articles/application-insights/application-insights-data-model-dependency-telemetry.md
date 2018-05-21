---
title: Модель данных телеметрии Azure Application Insights — телеметрия зависимостей | Документы Майкрософт
description: Модель данных Application Insights для телеметрии зависимостей
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 019b24839c20e7f8f46eeccf4a7b9622d18b0ad6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Телеметрия зависимостей: модель данных Application Insights

Телеметрия зависимостей (в [Application Insights](app-insights-overview.md)) представляет взаимодействие отслеживаемого компонента с удаленным, таким как SQL или конечная точка HTTP.

## <a name="name"></a>ИМЯ

Имя команды, инициированной этим вызовом зависимости. Низкое значение кратности. Примерами являются имя хранимой процедуры и шаблон пути URL-адреса.

## <a name="id"></a>ИД

Идентификатор для экземпляра вызова зависимости. Используется для корреляции с элементом телеметрии запроса, соответствующим этому вызову зависимости. Дополнительные сведения см. на странице [корреляции](application-insights-correlation.md).

## <a name="data"></a>Данные

Команда, инициированная этим вызовом зависимости. Примерами являются оператор SQL и HTTP URL со всеми параметрами запроса.

## <a name="type"></a>type

Имя типа зависимости. Низкое значение кратности для логической группировки зависимостей и интерпретации других полей, таких как commandName и resultCode. Примерами являются SQL, таблица Azure и HTTP.

## <a name="target"></a>Цель

Целевой сайт вызова зависимости. Примерами являются имя сервера, адрес узла. Дополнительные сведения см. на странице [корреляции](application-insights-correlation.md).

## <a name="duration"></a>Duration

Длительность запроса в формате: `DD.HH:MM:SS.MMMMMM`. Должно быть меньше `1000` дн.

## <a name="result-code"></a>Код результата

Код результата для вызова зависимости. Примерами являются код ошибки SQL и код состояния HTTP.

## <a name="success"></a>Успешно

Указание того, был ли вызов успешным.

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Дополнительная информация

- Настройка отслеживания зависимостей для платформы [.NET](app-insights-asp-net-dependencies.md)
- Настройка отслеживания зависимостей для платформы [Java](app-insights-java-agent.md)
- [Создание телеметрии настраиваемых зависимостей](app-insights-api-custom-events-metrics.md#trackdependency)
- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Ознакомление с [платформами](app-insights-platforms.md), поддерживаемыми Application Insights.
