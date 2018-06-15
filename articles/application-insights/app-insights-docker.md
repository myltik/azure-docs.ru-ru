---
title: Мониторинг приложений Docker в Azure Application Insights | Документация Майкрософт
description: Счетчики производительности, события и исключения Docker могут отображаться в Application Insights вместе с данными телеметрии из контейнерных приложений.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 53ade76b9dbdc27df90da1f7e197464816529d1d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295767"
---
# <a name="monitor-docker-applications-in-application-insights"></a>Мониторинг приложений Docker в Application Insights
События жизненного цикла и счетчики производительности из контейнеров [Docker](https://www.docker.com/) можно выводить в виде диаграмм в Application Insights. Установите образ [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) в контейнер в узле. Он будет отображать счетчики производительности для этого узла, а также для других образов.

С помощью Docker ваши приложения распространяются в упрощенных контейнерах со всеми зависимостями. Их можно запустить на любом хост-компьютере с модулем Docker.

При запуске [образа Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) в узле Docker вы получите следующие преимущества.

* Сведения о телеметрии жизненного цикла для всех контейнеров, запущенных на узле, — запуск, остановка и т. д.
* Счетчики производительности для всех контейнеров. ЦП, память, использование сети и многое другое.
* Если вы [установили пакет SDK для Application Insights для Java](app-insights-java-live.md) в приложениях, выполняющихся в контейнерах, у всех данных телеметрии этих приложений будут дополнительные свойства, идентифицирующие контейнер и хост-компьютер. Например, если имеются экземпляры приложения, запущенные на нескольких узлах, вы легко сможете отфильтровать данные телеметрии приложения по узлу.

![пример](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Настройка ресурса Application Insights
1. Выполните вход на [портал Microsoft Azure](https://azure.com) и [создайте ресурс Application Insights](app-insights-create-new-resource.md) для своего приложения или откройте имеющийся. 
   
    *Какой ресурс использовать?* Если приложения, которые выполняются на узле, были созданы другим разработчиком, вам потребуется [создать новый ресурс Application Insights](app-insights-create-new-resource.md). Там вы можете просматривать и анализировать данные телеметрии. (Выберите тип приложения "Общее".)
   
    Но если разработчиком приложений являетесь вы, мы надеемся, что вы [добавили пакет SDK для Application Insights](app-insights-java-live.md) в каждое из них. Если все они действительно являются компонентами одного бизнес-приложения, вы можете настроить их на отправку данных телеметрии в один ресурс, а затем использовать этот ресурс для отображения данных о производительности и жизненном цикле Docker. 
   
    Третий сценарий — вы разработали большинство приложений, но используете отдельные ресурсы для отображения их телеметрии. В этом случае, возможно, вам потребуется создать отдельный ресурс для данных Docker. 
2. Добавьте плитку Docker. Выберите **Добавить плитку**, перетащите плитку Docker из коллекции, а затем нажмите кнопку **Готово**. 
   
    ![пример](./media/app-insights-docker/03.png)

> [!NOTE]
> Сейчас панель обзора в Application Insights заблокирована и не позволяет добавлять плитки из коллекции. Вы сможете добавлять плитки Docker, как описано выше, с помощью интерфейса панели мониторинга Azure.

3. Щелкните раскрывающийся список **Основные компоненты** и скопируйте ключ инструментирования. Этот ключ указывает пакету SDK, куда отправлять данные телеметрии.

    ![пример](./media/app-insights-docker/02-props.png)

Не закрывайте это окно браузера: скоро вы будете просматривать в нем данные телеметрии.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Запуск монитора Application Insights на узле
Теперь, когда вы создали ресурс для отображения данных телеметрии, можно настроить контейнерное приложение, которое будет собирать и отправлять их.

1. Подключитесь к узлу Docker. 
2. Включите свой ключ инструментирования в следующую команду и выполните ее:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Для каждого узла Docker требуется только один образ Application Insights. Если приложение развертывается на нескольких узлах Docker, выполните эту команду на каждом узле.

## <a name="update-your-app"></a>Обновление приложения
Если приложение инструментируется с помощью [пакета SDK для Application Insights для Java](app-insights-java-get-started.md), добавьте следующую строку в файл ApplicationInsights.xml в проекте после элемента `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Это добавляет сведения телеметрии Docker, такие как контейнер и идентификатор узла, в каждый элемент, отправляемый из приложения.

## <a name="view-your-telemetry"></a>Просмотр телеметрии
Вернитесь к ресурсу Application Insights на портале Azure.

Щелкните плитку Docker.

Вскоре вы увидите данные, поступающие из приложения Docker, особенно при наличии других контейнеров, работающих в модуле Docker.

Ниже приведены примеры представлений данных.

### <a name="perf-counters-by-host-activity-by-image"></a>Счетчики производительности узла, активность образа
![пример](./media/app-insights-docker/10.png)

![пример](./media/app-insights-docker/11.png)

Щелкните имя любого узла или образа, чтобы получить подробные данные.

Чтобы настроить представление, щелкните любую диаграмму, заголовок сетки или нажмите кнопку «Добавить диаграмму». 

[Ознакомьтесь с дополнительными сведениями об обозревателе метрик](app-insights-metrics-explorer.md).

### <a name="docker-container-events"></a>События контейнера Docker
![пример](./media/app-insights-docker/13.png)

Для анализа отдельных событий щелкните [Поиск](app-insights-diagnostic-search.md). Найдите и отфильтруйте нужные вам события. Щелкните любое событие, чтобы просмотреть подробные данные.

### <a name="exceptions-by-container-name"></a>Исключения по имени контейнера
![пример](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Добавление контекста Docker в телеметрию приложения
Запрос данных телеметрии, отправленный из приложения, инструментированного с помощью пакета SDK AI, с дополнительным контекстом Docker:

![пример](./media/app-insights-docker/16.png)

Счетчики производительности для загруженности процессора и доступной памяти, насыщенные, дополненные и сгруппированные по имени контейнера Docker:

![пример](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Вопросы и ответы
*Какие возможности Application Insights отсутствуют в Docker?*

* Подробные показатели счетчиков производительности по контейнерам и образам.
* Интегрированные данные контейнера и приложения на одной панели мониторинга.
* [Экспорт данных телеметрии](app-insights-export-telemetry.md) в базу данных, Power BI или другую панель мониторинга для дальнейшего анализа.

*Как получить данные телеметрии из самого приложения?*

* Установите пакет SDK Application Insights в приложении. Узнайте, как это сделать в [веб-приложениях Java](app-insights-java-get-started.md) и [Windows](app-insights-asp-net.md).

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Дополнительная информация

* [Application Insights для Java](app-insights-java-get-started.md)
* [Application Insights для Node.js](app-insights-nodejs.md)
* [Application Insights для ASP.NET](app-insights-asp-net.md)
