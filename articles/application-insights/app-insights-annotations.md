<properties
    pageTitle="Заметки развертывания для Application Insights | Microsoft Azure"
    description="Добавление маркеров развертывания или сборки для диаграмм обозревателя метрик в Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="01/05/2016"
    ms.author="awills"/>

# Заметки к выпуску в Application Insights

Заметки к выпуску на диаграммах [обозревателя метрик](app-insights-metrics-explorer.md) показывают, где развернута новая сборка. С их помощью легко увидеть, повлияли ли ваши изменения на производительность приложения. Заметки к выпуску могут быть созданы автоматически [системой сборки Visual Studio Team Services](https://www.visualstudio.com/ru-RU/get-started/build/build-your-app-vs).

![Пример заметок с видимой корреляцией с временем ответа сервера](./media/app-insights-annotations/00.png)

Заметки к выпуску являются функцией службы облачной сборки и выпуска Visual Studio Team Services.

## Установка расширения заметок (однократно)

Чтобы получить возможность создания заметок к выпуску, необходимо установить одно из расширений Team Service, доступных в магазине Visual Studio.

1. Войдите в свой проект в [Visual Studio Team Services](https://www.visualstudio.com/ru-RU/get-started/setup/sign-up-for-visual-studio-online).
2. Откройте магазин Visual Studio, найдите расширение аннотаций Application Insights и добавьте его к своей учетной записи Team Services.

![В верхнем правом углу веб-страницы Team Services откройте "Магазин". Найдите и установите "Заметки Application Insights" со своей учетной записью.](./media/app-insights-annotations/10.png)

Это необходимо сделать только один раз для учетной записи Visual Studio Team Services. Теперь можно настроить заметки к выпуску для любого проекта в вашей учетной записи.



## Добавление задачи заметки к шаблону выпуска

Необходимо сделать это для каждого шаблона выпуска, в котором должны создаваться заметки к выпуску.

Откройте (или создайте) шаблон выпуска, который управляет развертываниями из Visual Studio Team Services.

Добавьте задачу и выберите в меню задачу заметок к выпуску Application Insights.

![В верхнем правом углу веб-страницы Team Services откройте "Магазин". Найдите и установите "Заметки Application Insights" со своей учетной записью.](./media/app-insights-annotations/40.png)

Для завершения этого этапа вам потребуются некоторые сведения от ресурса Application Insights, который используется для мониторинга приложения.

Во время получения сведений от Application Insights оставьте окно Team Services открытым.

## Скопируйте ключ API из Application Insights.

В отдельном окне браузера:

1. Выполните вход в [портал Microsoft Azure](https://portal.azure.com) и откройте ресурс Application Insights, который используется для мониторинга вашего приложения. (Или [создайте новый](app-insights-overview.md), если вы этого еще не сделали.)
2. Откройте раскрывающийся список **Основные компоненты** и скопируйте идентификатор подписки, группу ресурсов и имя ресурса для задачи заметок к выпуску. ![](./media/app-insights-annotations/50.png)
2. Откройте **Параметры**, **Ключи API** и создайте новый ключ. Скопируйте его в ![](./media/app-insights-annotations/30.png).

Наконец, **сохраните** определение выпуска.

## Маркеры развертывания

Теперь при каждом развертывании нового выпуска с помощью шаблона выпуска заметки будут отправляться в Application Insights. Заметки будут отображаться на диаграммах в обозревателе метрик.

<!---HONumber=AcomDC_0107_2016-->