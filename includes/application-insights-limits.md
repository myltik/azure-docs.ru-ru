Количество метрик и событий, используемых в приложении (то есть на ключ инструментирования), ограничено. Ограничения зависят от выбранного [ценового плана](https://azure.microsoft.com/pricing/details/application-insights/).

| Ресурс | Ограничение по умолчанию | Примечание
| --- | --- | --- |
| Общий объем данных в день | 100 ГБ | Объем данных можно сократить, задав ограничение. Если требуется больше данных, на портале можно увеличить граничное значение до 1000 ГБ. Если требуется объем более 1000 ГБ, отправьте сообщение электронной почты по адресу AIDataCap@microsoft.com.
| Бесплатный объем данных (в месяц)<br/> (план ценовой категории "Базовый") | 1 GB | Дополнительный объем оплачивается погигабайтно.
| Регулирование | 32 000 событий в секунду | Ограничение измеряется каждую минуту.
| Хранение данных | 90 дней | Этот ресурс используется для [поиска](../articles/application-insights/app-insights-diagnostic-search.md), [аналитики](../articles/application-insights/app-insights-analytics.md) и [обозревателя метрик](../articles/application-insights/app-insights-metrics-explorer.md).
| Хранение подробных результатов [многошагового теста доступности](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 дней | Этот ресурс предоставляет подробные результаты каждого шага.
| Максимальный размер события | 64 000 | 
| Длина имен свойств и метрик | 150 | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Длина строки значения свойства | 8192 | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Длина сообщения трассировки и исключения | 10 000 | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Количество [тестов доступности](../articles/application-insights/app-insights-monitor-web-app-availability.md) для одного приложения | 100 |
| Хранение данных [профилировщика](../articles/application-insights/app-insights-profiler.md) | 5 дней |
| Отправляемые данные [профилировщика](../articles/application-insights/app-insights-profiler.md) в день | 10 ГБ |

Дополнительные сведения см. в статье [Управление ценами и квотами для Application Insights](../articles/application-insights/app-insights-pricing.md).

