Количество метрик и событий, используемых в приложении (то есть на ключ инструментирования), ограничено.

Ограничения зависят от выбранной [ценовой категории](https://azure.microsoft.com/pricing/details/application-insights/).

| **Ресурс** | **Ограничение по умолчанию** | **Максимальное ограничение** |
| --- | --- | --- |
| Число точек данных сеанса<sup>1, 2</sup> в месяц |без ограничений | |
| Общее число точек данных в месяц для запроса, события, зависимости, трассировки, исключения и просмотра страницы |5 млн |50 млн<sup>3</sup> |
| Скорость передачи данных [трассировки и журнала](../articles/application-insights/app-insights-search-diagnostic-logs.md) |200 точек данных в секунду |500 точек данных в секунду |
| Скорость передачи данных [исключения](../articles/application-insights/app-insights-asp-net-exceptions.md) |50 точек данных в секунду |50 точек данных в секунду |
| Общая скорость передачи данных телеметрии для запроса, события, зависимости и просмотра страницы |200 точек данных в секунду |500 точек данных в секунду |
| Хранение необработанных данных для функций [Поиск](../articles/application-insights/app-insights-diagnostic-search.md) и [Аналитика](../articles/application-insights/app-insights-analytics.md) |7 дней | |
| Хранение объединенных данных для [обозревателя метрик](../articles/application-insights/app-insights-metrics-explorer.md) |90 дней | |
| Количество имен [свойств](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) |100 | |
| Длина имени свойства |150 | |
| Длина значения свойства |8192 | |
| Длина сообщения трассировки и исключения |10 000 | |
| Количество имен [метрик](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) |100 | |
| Длина имени метрики |150 | |
| [Тесты доступности](../articles/application-insights/app-insights-monitor-web-app-availability.md) |10 | |

<sup>1</sup> Точка данных — отдельное значение или событие метрики с прикрепленными свойствами и данными измерений.

<sup>2</sup> Для точки данных сеанса регистрируется начало или завершение сеанса, а также удостоверение пользователя.

<sup>3</sup> Можно приобрести дополнительную емкость свыше 50 млн точек данных.

[Цены и квоты для Application Insights](../articles/application-insights/app-insights-pricing.md)

<!---HONumber=AcomDC_0803_2016-->