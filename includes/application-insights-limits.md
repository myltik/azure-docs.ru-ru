Количество метрик и событий, используемых в приложении (то есть на ключ инструментирования), ограничено. Ограничения зависят от выбранного [ценового плана](https://azure.microsoft.com/pricing/details/application-insights/).

| **Ресурс** | **Ограничение по умолчанию** | **Примечание.**
| --- | --- | --- |
| Общий объем данных в день | 500 ГБ | Объем данных можно сократить, задав ограничение. Если вам необходимо больше, отправьте письмо по адресу AIDataCap@microsoft.com.
| Бесплатный объем данных (в месяц)<br/> (ценовой план "Базовый") | 1 GB | Дополнительный объем оплачивается погигабайтно.
| Регулирование | 32 000 событий в секунду | Ограничение измеряется каждую минуту.
| Хранение данных | 90 дней | Этот ресурс используется для [поиска](../articles/application-insights/app-insights-diagnostic-search.md), [аналитики](../articles/application-insights/app-insights-analytics.md) и [обозревателя метрик](../articles/application-insights/app-insights-metrics-explorer.md).
| Хранение подробных результатов [многошагового теста доступности](../articles/application-insights/app-insights-monitor-web-app-availability.md#multi-step-web-tests) | 90 дней | Этот ресурс предоставляет подробные результаты каждого шага.
| Длина имен свойств и метрик | 150 |
| Длина строки значения свойства | 8192 |
| Длина сообщения трассировки и исключения | 10 000 |
| Количество [тестов доступности](../articles/application-insights/app-insights-monitor-web-app-availability.md) для одного приложения  | 10 |

Дополнительные сведения см. в статье [Управление ценами и квотами для Application Insights](../articles/application-insights/app-insights-pricing.md).


<!--HONumber=Feb17_HO2-->


