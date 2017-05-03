---
title: "Модель данных телеметрии Azure Application Insights — телеметрия зависимостей | Документы Майкрософт"
description: "Модель данных Application Insights для телеметрии зависимостей"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: b86b1e1ff7c0370918b2509f98ad65265eb4186e
ms.lasthandoff: 04/22/2017


---
# <a name="dependency-telemetry-application-insights-data-model"></a>Телеметрия зависимостей: модель данных Application Insights

Телеметрия зависимостей представляет взаимодействие отслеживаемого компонента с удаленным, таким как SQL или конечная точка HTTP.

## <a name="name"></a>Имя

Имя команды, инициированной этим вызовом зависимости. Низкое значение кратности. Примерами являются имя хранимой процедуры и шаблон пути URL-адреса.

## <a name="id"></a>ИД

Идентификатор для экземпляра вызова зависимости. Используется для корреляции с элементом телеметрии запроса, соответствующим этому вызову зависимости. Дополнительные сведения см. на странице [корреляции](/correlation.md).

## <a name="data"></a>Данные

Команда, инициированная этим вызовом зависимости. Примерами являются оператор SQL и HTTP URL со всеми параметрами запроса.

## <a name="type"></a>Тип

Имя типа зависимости. Низкое значение кратности для логической группировки зависимостей и интерпретации других полей, таких как commandName и resultCode. Примерами являются SQL, таблица Azure и HTTP.

## <a name="target"></a>Цель

Целевой сайт вызова зависимости. Примерами являются имя сервера, адрес узла. Дополнительные сведения см. на странице [корреляции](/correlation.md).

## <a name="duration"></a>Длительность

Длительность запроса в формате: `DD.HH:MM:SS.MMMMMM`. Должно быть меньше `1000` дн.

## <a name="result-code"></a>Код результата

Код результата для вызова зависимости. Примерами являются код ошибки SQL и код состояния HTTP.

## <a name="success"></a>Успешно

Указание того, был ли вызов успешным.

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Дальнейшие действия

- В [этой статье](/application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Настройка отслеживания зависимостей для платформы [.NET](/app-insights-asp-net-dependencies.md)
- Настройка отслеживания зависимостей для платформы [Java](/app-insights-java-agent.md)
- Ознакомление с [платформами](/app-insights-platforms.md), поддерживаемыми Application Insights.

