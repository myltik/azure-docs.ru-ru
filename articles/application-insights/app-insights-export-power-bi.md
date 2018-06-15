---
title: Экспорт в Power BI из Azure Application Insights | Документация Майкрософт
description: Аналитические запросы можно просматривать в Power BI.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: dee3313082fbe75d76bf27105979cf7e869fafad
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294128"
---
# <a name="feed-power-bi-from-application-insights"></a>Использование данных Application Insights в Power BI
[Power BI](http://www.powerbi.com/) — это набор бизнес-инструментов для анализа данных и обмена сведениями. На каждом устройстве доступны панели мониторинга с широкими возможностями. Вы можете объединять данные из различных источников, в том числе аналитические запросы из [ Application Insights](app-insights-overview.md).

Существуют три рекомендуемых способа экспортировать данные Application Insights в Power BI. Их можно использовать отдельно или совместно.

* [**Использование адаптера Power BI**](#power-pi-adapter). С его помощью можно настроить панель мониторинга телеметрии из своего приложения. Здесь есть предопределенный набор диаграмм, но можно добавлять свои запросы из других источников.
* [**Экспорт запросов Analytics**](#export-analytics-queries). Можно создать нужный запрос и экспортировать его в Power BI. Можно написать собственный запрос с помощью Analytics или создать его с помощью воронок данных об использовании. Его можно разместить на панели мониторинга вместе с другими данными.
* [**Непрерывный экспорт и Azure Stream Analytics**](app-insights-export-stream-analytics.md). Этот метод удобен, если необходимо хранить данные в течение долгого времени. Если это не требуется, используйте другой метод, так как настройка этого требует больше усилий.

## <a name="power-bi-adapter"></a>Использование адаптера Power BI
Этот метод предполагает создание панели мониторинга телеметрии. Предусматривается первоначальный набор данных, но к нему можно добавлять больше сведений.

### <a name="get-the-adapter"></a>Получение адаптера
1. Войдите в [Power BI](https://app.powerbi.com/).
2. Выберите **Получение данных**, **Службы** и **Application Insights**.
   
    ![Снимки экрана получения данных из источника данных Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Укажите сведения о ресурсе Application Insights.
   
    ![Снимок экрана получения данных из источника данных Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Дождитесь завершения импорта данных.
   
    ![Снимок экрана адаптера Power BI](./media/app-insights-export-power-bi/010.png)

Панель мониторинга можно настраивать, объединяя диаграммы Application Insights с диаграммами из других источников и с аналитическими запросами. Можно получить дополнительные диаграммы из коллекции визуализаций, и у каждой диаграммы есть параметры, которые можно задать.

После первоначального импорта панель мониторинга и отчеты продолжают обновляться ежедневно. При этом можно управлять расписанием обновления для набора данных.

## <a name="export-analytics-queries"></a>Экспорт запросов аналитики
Этот способ предполагает написание любого запроса Analytics или его экспорт из воронок данных об использовании, а затем его экспорт на панель мониторинга Power BI. (Его можно добавить на панель мониторинга, созданную при помощи адаптера.)

### <a name="one-time-install-power-bi-desktop"></a>Короткий путь: установка Power BI Desktop
Чтобы импортировать запрос Application Insights, используйте версию Power BI для компьютера. Затем запрос можно опубликовать в Интернете или в облачной рабочей области Power BI. 

Установите [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Экспорт запроса аналитики
1. [Откройте средство аналитики и напишите запрос](app-insights-analytics-tour.md).
2. Протестируйте запрос и откорректируйте его до необходимой степени. Прежде чем экспортировать запрос, убедитесь, что он выполняется в Analytics надлежащим образом.
3. В меню **Export** (Экспорт) выберите пункт **Power BI (M)**. Сохраните текстовый файл.
   
    ![Снимок экрана Analytics, на котором выделено меню "Экспорт"](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Откройте Power BI Desktop и выберите **Получение данных** > **Пустой запрос**. Затем в редакторе запросов в разделе **Вид**выберите **Расширенный редактор**.

    Вставьте экспортированный сценарий на языке M в расширенный редактор.

    ![Снимок экрана Power BI Desktop, на котором выделен расширенный редактор](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Чтобы разрешить Power BI доступ к Azure, может потребоваться указать учетные данные. Щелкните **Учетная запись в организации**, чтобы войти, используя свою учетную запись Майкрософт.
   
    ![Снимок экрана диалогового окна "Параметры запроса" в Power BI](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Если нужно проверить учетные данные, используйте команду меню **Параметры источника данных** в редакторе запросов. Укажите учетные данные, используемые для Azure. Они могут отличаться от учетных данных для Power BI.
2. Выберите визуализацию для запроса и выберите поля для оси X, оси Y и размерности.
   
    ![Снимок экрана параметров визуализации в Power BI Desktop](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Опубликуйте отчет в рабочей области облака Power BI. Из нее можно внедрять синхронизированную версию на другие веб-страницы.
   
    ![Снимок экрана Power BI Desktop, на котором выделена кнопка "Опубликовать"](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Периодически обновляйте отчет вручную или настройте запланированное обновление на странице параметров.

### <a name="export-a-funnel"></a>Экспорт воронки
1. [Создайте воронку](usage-funnels.md).
2. Выберите **Power BI**. 

   ![Снимок экрана с кнопкой Power BI](./media/app-insights-export-power-bi/button.png)
   
3. Откройте Power BI Desktop и выберите **Получение данных** > **Пустой запрос**. Затем в редакторе запросов в разделе **Вид**выберите **Расширенный редактор**.

   ![Снимок экрана Power BI Desktop, на котором выделена кнопка "Пустой запрос"](./media/app-insights-export-power-bi/blankquery.png)

   Вставьте экспортированный сценарий на языке M в расширенный редактор. 

   ![Снимок экрана Power BI Desktop, на котором выделен расширенный редактор](./media/app-insights-export-power-bi/advancedquery.png)

4. Выберите элементы запроса, а затем — визуализацию "Воронка".

   ![Снимок экрана параметров визуализации в Power BI Desktop](./media/app-insights-export-power-bi/selectsequence.png)

5. Замените заголовок на информативный и опубликуйте отчет в облачной рабочей области Power BI. 

   ![Снимок экрана Power BI Desktop, на котором выделен измененный заголовок](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Устранение неполадок

Могут возникнуть ошибки, относящиеся к учетным данным или размеру набора данных. Ниже приведены сведения о том, что нужно делать в случае возникновения таких ошибок.

### <a name="unauthorized-401-or-403"></a>Недостаточно прав (401 или 403)
Такая ошибка может произойти, если токен обновления не обновлен. Попробуйте выполнить следующие действия, чтобы убедиться, что у вас по-прежнему есть доступ.

1. Войдите на портал Azure и убедитесь, что можете получить доступ к ресурсу.
2. Попробуйте обновить учетные данные для панели мониторинга.

 Если у вас есть доступ, но обновление учетных данных не выполняется, обратитесь в службу поддержки.

### <a name="bad-gateway-502"></a>Недопустимый шлюз (502)
Обычно это вызвано запросом аналитики, который возвращает слишком много данных. Попробуйте использовать меньший диапазон времени для запроса. 

Если уменьшение набора данных, поступающих от запроса Analytics, не соответствует вашим требованиям, рассмотрите использование [API](https://dev.applicationinsights.io/documentation/overview) для извлечения набора данных большего размера. Ниже приведены инструкции по преобразованию экспорта запросов на языке M для использования API.

1. Создайте [ключ API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Обновите сценарий Power BI на языке M, экспортированный из Analytics, заменив URL-адрес Azure Resource Manager интерфейсом API Application Insights.
   * Вместо **https://management.azure.com/subscriptions/...**
   * укажите  **https://api.applicationinsights.io/beta/apps/...**
3. Наконец, измените учетные данные на базовые и используйте свой ключ API.
  

**Существующий сценарий**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Обновленный сценарий**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Дополнительная информация о выборке
Если ваше приложение отправляет большие объемы данных, может потребоваться использовать функцию адаптивной выборки, которая отправляет только часть данных телеметрии. То же произойдет, если выборка настроена вручную в пакете SDK или на приеме. [Дополнительная информация о выборке](app-insights-sampling.md).


## <a name="next-steps"></a>Дополнительная информация
* [Дополнительные сведения о Power BI](http://www.powerbi.com/learning/)
* [Руководство по аналитике](app-insights-analytics-tour.md)

