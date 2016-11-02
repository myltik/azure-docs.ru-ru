<properties 
	pageTitle="Пошаговое руководство. Отслеживание работы Microsoft Dynamics CRM с помощью Application Insights" 
	description="Получение данных телеметрии из Microsoft Dynamics CRM Online с помощью Application Insights. Пошаговое руководство по настройке, получению данных, визуализации и экспорту." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="awills"/>
 
# Пошаговое руководство. Включение телеметрии для Microsoft Dynamics CRM Online с помощью Application Insights

В этой статье показано, как получить данные телеметрии из службы [Microsoft Dynamics CRM Online](https://www.dynamics.com/) с помощью [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/). Мы рассмотрим весь процесс добавления сценария Application Insights в приложение, сбор данных и их визуализацию.

>[AZURE.NOTE] [Browse the sample solution](https://dynamicsandappinsights.codeplex.com/).

## Добавление Application Insights в новый или существующий экземпляр CRM Online 

Чтобы отслеживать работу приложения, добавьте в него пакет SDK для Application Insights. Пакет SDK отправляет данные телеметрии на [портал Application Insights](https://portal.azure.com), где вы можете использовать наши эффективные инструменты анализа и диагностики, а также экспортировать данные в хранилище.

### Создание ресурса Application Insights в Azure

1. Получите [учетную запись Microsoft Azure](http://azure.com/pricing). 
2. Войдите на [портал Azure](https://portal.azure.com) и добавьте новый ресурс Application Insights. Здесь будут обрабатываться и отображаться ваши данные.

    ![Щелкните значок «+» и последовательно выберите «Службы для разработчиков», Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Выберите приложение ASP.NET в качестве типа приложения.

3. Откройте вкладку «Быстрый запуск», а затем —сценарий кода.

    ![](./media/app-insights-sample-mscrm/03.png)

**Не закрывайте страницу с кодом**, выполняя следующий шаг в другом окне браузера. Код вскоре вам понадобится.

### Создание веб-ресурса JavaScript в Microsoft Dynamics CRM

1. Откройте экземпляр CRM Online и войдите с правами администратора.
2. Последовательно откройте элементы "Параметры Microsoft Dynamics CRM", "Настройка", "Настройка системы".

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Создайте ресурс JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Присвойте ему имя, выберите тип **Script (JScript)** и откройте текстовый редактор.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Скопируйте код из Application Insights. При копировании необходимо пропускать теги скриптов. См. ниже снимок экрана.

    ![](./media/app-insights-sample-mscrm/09.png)

    Код содержит ключ инструментирования, который идентифицирует ресурс Application Insights.

5. Щелкните «Сохранить», а затем «Опубликовать».

    ![](./media/app-insights-sample-mscrm/10.png)

### Формы инструментов

1. В Microsoft CRM Online откройте форму «Учетная запись».

    ![](./media/app-insights-sample-mscrm/11.png)

2. Откройте свойства формы.

    ![](./media/app-insights-sample-mscrm/12.png)

3. Добавьте созданный веб-ресурс JavaScript.

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Сохраните и опубликуйте настройки формы.


## Собранные показатели телеметрии

Теперь сбор данных телеметрии для формы настроен. При каждом использовании данные будут отправляться в ресурс Application Insights.

Ниже приведены примеры отображаемых данных.

#### Работоспособность приложения

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Исключения браузера:

![](./media/app-insights-sample-mscrm/17.png)

Щелкните диаграмму, чтобы получить более подробную информацию.

![](./media/app-insights-sample-mscrm/18.png)

#### Использование

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### Браузеры

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### Географическое положение

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### Запрос на просмотр внутренней страницы

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## Пример кода

[Получите пример кода](https://dynamicsandappinsights.codeplex.com/).

## Power BI

Если [экспортировать данные в Microsoft Power BI](app-insights-export-power-bi.md), то можно выполнить еще более подробный анализ данных.

## Образец решения Microsoft Dynamics CRM

[Ниже приведен простой пример решения, реализованного в Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## Подробнее

* [Что такое Azure Application Insights?](app-insights-overview.md)
* [Application Insights для веб-страниц](app-insights-javascript.md)
* [Дополнительные примеры и пошаговые руководства](app-insights-code-samples.md)

 

<!---HONumber=AcomDC_1125_2015-->