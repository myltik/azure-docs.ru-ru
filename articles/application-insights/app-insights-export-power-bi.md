---
title: "Экспорт в Power BI из Application Insights | Документация Майкрософт"
description: "Аналитические запросы можно просматривать в Power BI."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 02c51e6a576b5a91044eae784c72d7529497b814
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017

---
# <a name="feed-power-bi-from-application-insights"></a>Использование данных Application Insights в Power BI
[Power BI](http://www.powerbi.com/) — это набор средств бизнес-аналитики для анализа данных и обмена сведениями. На каждом устройстве доступны панели мониторинга с широкими возможностями. Вы можете объединять данные из различных источников, в том числе аналитические запросы из [ Application Insights](app-insights-overview.md).

Существуют три рекомендуемых способа экспортировать данные Application Insights в Power BI. Их можно использовать отдельно или совместно.

* [**Использование адаптера Power BI** ](#power-pi-adapter). С его помощью можно настроить панель мониторинга телеметрии из своего приложения. Здесь есть предопределенный набор диаграмм, но можно добавлять свои запросы из других источников.
* [**Экспорт аналитических запросов**](#export-analytics-queries). Напишите необходимый запрос с помощью средства аналитики и экспортируйте этот запрос в Power BI. Его можно разместить на панели мониторинга вместе с другими данными.
* [**Непрерывный экспорт и Stream Analytics.**](app-insights-export-stream-analytics.md) Это самый трудоемкий метод. Он полезен, если необходимо хранить данные в течение долгого времени. В иных случаях рекомендуется использовать другие методы.

## <a name="power-bi-adapter"></a>Использование адаптера Power BI
Этот метод предполагает создание панели мониторинга телеметрии. Предусматривается первоначальный набор данных, но к нему можно добавлять больше сведений.

### <a name="get-the-adapter"></a>Получение адаптера
1. Войдите в [Power BI](https://app.powerbi.com/).
2. Выберите **Получение данных**, **Службы**, **Application Insights**.
   
    ![Получение из источника данных Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Укажите сведения о ресурсе Application Insights.
   
    ![Получение из источника данных Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Дождитесь завершения импорта данных.
   
    ![Использование адаптера Power BI](./media/app-insights-export-power-bi/010.png)

Панель мониторинга можно настраивать, объединяя диаграммы Application Insights с диаграммами из других источников и с аналитическими запросами. В коллекции визуализации доступны дополнительные диаграммы, и у каждой диаграммы есть параметры, которые можно задать.

После первоначального импорта панель мониторинга и отчеты продолжают обновляться ежедневно. При этом можно управлять расписанием обновления для набора данных.

## <a name="export-analytics-queries"></a>Экспорт запросов аналитики
Этот способ предполагает написание любого аналитического запроса, а затем его экспорт на панель мониторинга Power BI. (Его можно добавить на панель мониторинга, созданную при помощи адаптера.)

### <a name="one-time-install-power-bi-desktop"></a>Короткий путь: установка Power BI Desktop
Чтобы импортировать запрос Application Insights, используйте версию Power BI для компьютера. Затем запрос можно опубликовать в Интернете или в рабочей области облака Power BI. 

Установите [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Экспорт запроса аналитики
1. [Откройте средство аналитики и напишите запрос](app-insights-analytics-tour.md).
2. Протестируйте запрос и откорректируйте его до необходимой степени.

   **Прежде чем экспортировать запрос, убедитесь, что он выполняется в Analytics надлежащим образом.**
3. В меню **Export** (Экспорт) выберите пункт **Power BI (M)**. Сохраните текстовый файл.
   
    ![Экспорт запроса Power BI](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. В Power BI Desktop выберите меню **Get Data, Blank Query** (Получить данные, пустой запрос), а затем в редакторе запросов на вкладке **Представление** выберите **расширенный редактор запросов**.

    Вставьте скрипт на языке M в расширенный редактор запросов.

    ![Расширенный редактор запросов](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Чтобы разрешить Power BI доступ к Azure, может потребоваться указать учетные данные. Щелкните "Учетная запись организации", чтобы войти, используя свою учетную запись Майкрософт.
   
    ![Укажите учетные данные Azure, чтобы Power BI выполнила запрос Application Insights.](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Если нужно проверить учетные данные, используйте команду меню "Параметры источника данных" в редакторе запросов. Укажите учетные данные, используемые для Azure. Они могут отличаться от учетных данных для Power BI.)
2. Выберите визуализацию для запроса, измерение сегментирования, поля для осей X и Y.
   
    ![Выбор визуализации](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Опубликуйте отчет в рабочей области облака Power BI. Из нее можно внедрять синхронизированную версию на другие веб-страницы.
   
    ![Выбор визуализации](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Периодически обновляйте отчет вручную или настройте запланированное обновление на странице параметров.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="401-or-403-unauthorized"></a>401 или 403 — недостаточно прав 
Это может произойти, если токен обновления не обновлен. Попробуйте выполнить следующие действия, чтобы убедиться, что у вас по-прежнему есть доступ. Если у вас есть доступ, но обновление учетных данных не работает, обратитесь в службу поддержки.

1. Войдите на портал Azure и убедитесь, что можете получить доступ к ресурсу.
2. Попробуйте обновить учетные данные для панели мониторинга.

### <a name="502-bad-gateway"></a>502 — Недопустимый шлюз
Обычно это вызвано запросом аналитики, который возвращает слишком много данных. Вначале следует попробовать использовать меньший диапазон времени или использовать функции [ago](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#ago) или [startofweek/startofmonth](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#startofweek) только для необходимых полей [проекта](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#project-operator).

Если уменьшение набора данных, поступающих от запросов аналитики, не соответствует вашим требованиям, можно использовать [API](https://dev.applicationinsights.io/documentation/overview) для извлечения набора данных большего размера. Ниже приведены инструкции по преобразованию экспорта запросов на языке M для использования API.

1. Создайте [ключ API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Обновите скрипт Power BI на языке M, экспортированный из аналитики, заменив URL-адрес ARM на API AI (см. пример ниже).
   * Замените **https://management.azure.com/subscriptions/...**
   * на **https://api.applicationinsights.io/beta/apps/...**
3. Наконец, обновите учетные данные на базовые и используйте ваш ключ API.
  

**Существующий скрипт**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Обновленный скрипт**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Дополнительная информация о выборке
Если ваше приложение отправляет большие объемы данных, может сработать функция адаптивной выборки и отправить только часть вашей телеметрии. То же произойдет, если выборка настроена вручную в пакете SDK или на приеме. [Дополнительная информация о выборке.](app-insights-sampling.md)


## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения о Power BI](http://www.powerbi.com/learning/)
* [Руководство по аналитике](app-insights-analytics-tour.md)


