---
title: Взаимодействие с отчетами с помощью интерфейса API JavaScript | Microsoft Docs
description: Power BI Embedded, взаимодействие с отчетами с помощью интерфейса API JavaScript
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''

ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton

---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Взаимодействие с отчетами Power BI с помощью интерфейса API JavaScript
Интерфейс API JavaScript службы Power BI позволяет с легкостью внедрять отчеты Power BI в приложения. С помощью API ваши приложения могут программно взаимодействовать с разными элементами отчетов, например со страницами и фильтрами. Эти интерактивные возможности обеспечивают более полную интеграцию отчетов Power BI с приложениями.

Чтобы внедрить отчет Power BI в приложение, воспользуйтесь плавающим фреймом, размещенным как часть приложения. Как показано на рисунке ниже, плавающий фрейм выполняет роль границы между приложением и отчетом. 

![Плавающий фрейм Power BI Embedded без интерфейса API JavaScript](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-1.png)

Плавающий фрейм значительно упрощает внедрение, но без интерфейса API JavaScript отчет и приложение не смогут взаимодействовать друг с другом. Из-за этого отчет не будет восприниматься как часть приложения. Между отчетом и приложением должен происходить обмен данными (см. рисунок ниже).

![Плавающий фрейм Power BI Embedded c интерфейсом API JavaScript](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-2.png)

Интерфейс API JavaScript службы Power BI позволяет написать код, который может безопасно передаваться через границу плавающего фрейма. Это позволяет приложению программно выполнить действие в отчете и прослушать события, возникающие в результате действий пользователя в отчете.

## <a name="what-can-you-do-with-the-power-bi-javascript-api?"></a>Что можно сделать с помощью интерфейса API JavaScript службы Power BI?
С помощью интерфейса API JavaScript вы можете управлять отчетами, переходить на те или иные страницы отчетов, фильтровать отчеты и управлять событиями внедрения. Структура интерфейса API показана на схеме ниже.

![Схема интерфейса API JavaScript службы Power BI](media\\powerbi-embedded-interact-with-reports\\powerbi-embedded-interact-report-3.png)

### <a name="manage-reports"></a>Управление отчетами
Интерфейс API JavaScript дает возможность управлять поведением на уровне отчета и страницы.

* Безопасное внедрение отчетов Power BI в приложение (попробуйте воспользоваться [демонстрационным приложением внедрения](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Задание маркера доступа.
* Настройка отчета.
  * Включение и отключение панели фильтров и панели навигации по страницам (попробуйте воспользоваться [демонстрационным приложением обновления параметров](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Установка значений по умолчанию для страниц и фильтров (попробуйте воспользоваться [демонстрацией задания значений по умолчанию](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Включение и выключение полноэкранного режима.

[Дополнительные сведения о внедрении отчетов.](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Навигация по страницам отчета
Интерфейс API JavaScript позволяет обнаруживать все страницы в отчете и задавать текущую страницу. Попробуйте воспользоваться [демонстрационным приложением навигации](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Дополнительные сведения о навигации по страницам.](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Фильтрация данных в отчете
Интерфейс API JavaScript предоставляет базовые и расширенные возможности фильтрации для внедренных отчетов и страниц отчетов. Попробуйте [демонстрационное приложение фильтрации](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)и ознакомьтесь с базовым кодом.  

#### <a name="basic-filters"></a>Базовые фильтры
Базовый фильтр размещается на уровне столбца или иерархии и содержит ряд значений, которые нужно добавить или исключить.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Расширенные фильтры
Расширенные фильтры используют логические операторы И и ИЛИ и могут принимать одно или два условия, в каждом из которых есть свой оператор и значение. Поддерживаются такие условия:

* None
* LessThan;
* LessThanOrEqual;
* GreaterThan
* GreaterThanOrEqual;
* Содержит
* DoesNotContain;
* StartsWith;
* DoesNotStartWith;
* Is;
* IsNot;
* IsBlank;
* IsNotBlank.

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Дополнительные сведения о фильтрации.](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Обработка событий
Приложение может не только отправлять информацию в плавающий фрейм, но и получать от него информацию о следующих событиях:

* внедрение;
  * загрузка;
  * error
* Отчеты
  * изменение страницы;
  * выбор данных (ожидается в ближайшее время).

[Дополнительные сведения об обработке событий.](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об интерфейсе API JavaScript службы Power BI доступны по таким ссылкам:

* [Вики-сайт по интерфейсу API JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Справочник по объектным моделям](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* Примеры
  * [Angular](http://azure-samples.github.io/powerbi-angular-client)
  * [Ember](https://github.com/Microsoft/powerbi-ember)
* [Демонстрация в реальном времени](https://microsoft.github.io/PowerBI-JavaScript/demo/)

<!--HONumber=Oct16_HO2-->


