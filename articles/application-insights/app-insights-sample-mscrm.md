---
title: "Пошаговое руководство. Отслеживание работы Microsoft Dynamics CRM с помощью Application Insights"
description: "Получение данных телеметрии из Microsoft Dynamics CRM Online с помощью Application Insights. Пошаговое руководство по настройке, получению данных, визуализации и экспорту."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 9304b26711226fc9a7e672f59441ae65c0d5a023


---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Пошаговое руководство. Включение телеметрии для Microsoft Dynamics CRM Online с помощью Application Insights
В этой статье показано, как получить данные телеметрии из службы [Microsoft Dynamics CRM Online](https://www.dynamics.com/) с помощью [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Мы рассмотрим весь процесс добавления сценария Application Insights в приложение, сбор данных и их визуализацию.

> [!NOTE]
> [Получите пример решения.](https://dynamicsandappinsights.codeplex.com/)
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Добавление Application Insights в новый или существующий экземпляр CRM Online
Чтобы отслеживать работу приложения, добавьте в него пакет SDK для Application Insights. Пакет SDK отправляет данные телеметрии на [портал Application Insights](https://portal.azure.com), где вы можете использовать наши эффективные инструменты анализа и диагностики, а также экспортировать данные в хранилище.

### <a name="create-an-application-insights-resource-in-azure"></a>Создание ресурса Application Insights в Azure
1. Получите [учетную запись Microsoft Azure](http://azure.com/pricing). 
2. Войдите на [портал Azure](https://portal.azure.com) и добавьте новый ресурс Application Insights. Здесь будут обрабатываться и отображаться ваши данные.
   
    ![Щелкните значок «+» и последовательно выберите «Службы для разработчиков», Application Insights.](./media/app-insights-sample-mscrm/01.png)
   
    Выберите приложение ASP.NET в качестве типа приложения.
3. Откройте вкладку «Быстрый запуск», а затем —сценарий кода.
   
    ![](./media/app-insights-sample-mscrm/03.png)

**Не закрывайте страницу с кодом** , выполняя следующий шаг в другом окне браузера. Код вскоре вам понадобится. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Создание веб-ресурса JavaScript в Microsoft Dynamics CRM
1. Откройте экземпляр CRM Online и войдите с правами администратора.
2. Последовательно откройте элементы "Параметры Microsoft Dynamics CRM", "Настройка", "Настройка системы".
   
    ![](./media/app-insights-sample-mscrm/04.png)
   
    ![](./media/app-insights-sample-mscrm/05.png)

    ![](./media/app-insights-sample-mscrm/06.png)

1. Создайте ресурс JavaScript.
   
    ![](./media/app-insights-sample-mscrm/07.png)
   
    Присвойте ему имя, выберите тип **Script (JScript)** и откройте текстовый редактор.
   
    ![](./media/app-insights-sample-mscrm/08.png)
2. Скопируйте код из Application Insights. При копировании необходимо пропускать теги скриптов. См. ниже снимок экрана.
   
    ![](./media/app-insights-sample-mscrm/09.png)
   
    Код содержит ключ инструментирования, который идентифицирует ресурс Application Insights.
3. Щелкните «Сохранить», а затем «Опубликовать».
   
    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Формы инструментов
1. В Microsoft CRM Online откройте форму «Учетная запись».
   
    ![](./media/app-insights-sample-mscrm/11.png)
2. Откройте свойства формы.
   
    ![](./media/app-insights-sample-mscrm/12.png)
3. Добавьте созданный веб-ресурс JavaScript.
   
    ![](./media/app-insights-sample-mscrm/13.png)
   
    ![](./media/app-insights-sample-mscrm/14.png)
4. Сохраните и опубликуйте настройки формы.

## <a name="metrics-captured"></a>Собранные показатели телеметрии
Теперь сбор данных телеметрии для формы настроен. При каждом использовании данные будут отправляться в ресурс Application Insights.

Ниже приведены примеры отображаемых данных.

#### <a name="application-health"></a>Работоспособность приложения
![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Исключения браузера:

![](./media/app-insights-sample-mscrm/17.png)

Щелкните диаграмму, чтобы получить более подробную информацию.

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Использование
![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Браузеры
![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Географическое положение
![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Запрос на просмотр внутренней страницы
![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Пример кода
[Получите пример кода](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Если [экспортировать данные в Microsoft Power BI](app-insights-export-power-bi.md), то можно выполнить еще более подробный анализ данных.

## <a name="sample-microsoft-dynamics-crm-solution"></a>Образец решения Microsoft Dynamics CRM
[Ниже приведен простой пример решения, реализованного в Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Подробнее
* [Что такое Azure Application Insights?](app-insights-overview.md)
* [Application Insights для веб-страниц](app-insights-javascript.md)
* [Дополнительные примеры и пошаговые руководства](app-insights-code-samples.md)




<!--HONumber=Nov16_HO3-->


