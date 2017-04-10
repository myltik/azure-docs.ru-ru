---
title: "Azure Application Insights для рабочих ролей и служб Windows | Документация Майкрософт"
description: "Добавьте вручную пакет SDK Application Insights в приложение ASP.NET для анализа использования, доступности и производительности."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 97d750035e79d4780738e660d05e1a41085d51f1
ms.lasthandoff: 03/31/2017


---
# <a name="manually-configure-application-insights-for-aspnet-applications"></a>Настройка Application Insights вручную для приложений ASP.NET
[Application Insights](app-insights-overview.md) — это расширяемый инструмент для разработчиков веб-ресурсов, позволяющий отслеживать производительность и использование работающего приложения. Вы можете вручную настроить это средство для мониторинга службы Windows, рабочих ролей и других приложений ASP.NET. Для веб-приложений в Visual Studio можно выбрать либо ручную, либо [автоматическую настройку](app-insights-asp-net.md) .

![Пример диаграмм мониторинга производительности](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Перед началом работы
Вам необходимы:

* подписка на [Microsoft Azure](http://azure.com). Если у вашей группы или организации есть подписка Azure, владелец может добавить вас в нее с помощью вашей [учетной записи Майкрософт](http://live.com).
* Visual Studio 2013 или более поздняя версия.

## <a name="add"></a>1. Создание ресурса Application Insights
Войдите на [портал Azure](https://portal.azure.com/)и создайте новый ресурс Application Insights. Выберите приложение ASP.NET в качестве типа приложения.

![Нажмите "Создать" и "Application Insights"](./media/app-insights-windows-services/01-new-asp.png)

[Ресурс](app-insights-resources-roles-access-control.md) в Azure — это экземпляр службы. В этом ресурсе будет анализироваться и представляться телеметрия из вашего приложения.

От выбранного типа приложения зависит содержимое по умолчанию столбцов ресурсов и свойств, отображаемых в [обозревателе метрик](app-insights-metrics-explorer.md).

#### <a name="copy-the-instrumentation-key"></a>Копирование ключа инструментирования
Ключ идентифицирует ресурс, и вы установите его в пакет SDK для направления данных ресурсу.

![Нажмите "Свойства", выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-windows-services/02-props-asp.png)

Действия по созданию нового ресурса, которые вы только что выполнили, являются хорошей отправной точкой для реализации мониторинга в любых приложениях. Теперь вы можете отправить на этот ресурс данные.

## <a name="sdk"></a>2. Установка пакета Application Insights в приложении
Установка и настройка пакета Application Insights зависит от платформы, на которой вы работаете. С приложениями ASP.NET все просто.

1. В Visual Studio измените пакеты NuGet вашего проекта веб-приложения.
   
    ![Щелкните проект правой кнопкой мыши и выберите пункт "Управление пакетами Nuget"](./media/app-insights-windows-services/03-nuget.png)
2. Установите пакет Application Insights для приложений службы Windows.
   
    ![Поиск Application Insights](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *Можно ли использовать другие пакеты?*
   
    Да. Выберите основной API (Microsoft.ApplicationInsights), если вы хотите использовать API для отправки собственной телеметрии. Пакет Windows Server автоматически включает основной API, а также ряд других пакетов, например для сбора данных счетчиков производительности и отслеживания зависимостей. 

#### <a name="to-upgrade-to-future-package-versions"></a>Обновление до будущих версий пакета
Время от времени мы выпускаем новую версию пакета SDK.

Чтобы выполнить обновление до [нового выпуска пакета](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), еще раз откройте диспетчер пакетов NuGet и выполните фильтрацию по установленным пакетам. Выберите **Microsoft.ApplicationInsights.WindowsServer**, а затем — **Обновить**.

Если были выполнены какие-либо настройки файла ApplicationInsights.config, то, прежде чем выполнять обновление, сохраните его копию, а затем объедините изменения в новой версии.

## <a name="3-send-telemetry"></a>3. Отправка данных телеметрии
**Если установлен только основной пакет API.**

* Задайте ключ инструментирования в коде, например в `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *ваш ключ* `";` 
* [Создайте собственную телеметрию с помощью](app-insights-api-custom-events-metrics.md#ikey).

**Если у вас установлены другие пакеты Application Insights** , ключ инструментирования можно задать с помощью CONFIG-файла:

* Отредактируйте файл ApplicationInsights.config (который был добавлен при установке NuGet). Вставьте следующий фрагмент непосредственно перед закрывающим тегом:
  
    `<InstrumentationKey>` *скопированный ключ инструментирования* `</InstrumentationKey>`
* Убедитесь, что свойства файла ApplicationInsights.config в обозревателе решений имеют следующие значения: **"Действие сборки = содержимое", "Копировать в выходной каталог = копировать"**.

Если вы хотите [изменить ключ для разных конфигураций сборки](app-insights-separate-resources.md), можно задать ключ инструментирования в коде. Если ключ задан в коде, его не нужно задавать в файле `.config`.

## <a name="run"></a>Запуск проекта
Запустите приложение, нажав клавишу **F5** , и попробуйте открывать разные страницы, чтобы создать некоторый объем данных телеметрии.

В Visual Studio вы увидите число отправленных событий.

![Количество событий в Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Просмотр своих данных телеметрии
Вернитесь на [портал Azure](https://portal.azure.com/) и перейдите к своему ресурсу Application Insights.

Выполните поиск данных в диаграммах "Обзор". Сначала вы увидите только одну или две точки. Например:

![Щелкните плитки, чтобы увидеть больше данных](./media/app-insights-windows-services/12-first-perf.png)

Щелкните любую диаграмму, чтобы увидеть более подробные метрики. [Дополнительные сведения о метриках.](app-insights-web-monitor-performance.md)

#### <a name="no-data"></a>Данные отсутствуют?
* Используйте приложение, открывая различные страницы, чтобы создать некоторый объем данных телеметрии.
* Откройте плитку [Поиск](app-insights-diagnostic-search.md) , чтобы просмотреть отдельные события. Иногда для прохождения событий через конвейер метрики требуется чуть больше времени.
* Подождите несколько секунд и нажмите **Обновить**. Диаграмма периодически обновляется, однако ее можно обновить и вручную, если вы ждете появления каких-либо данных.
* См. раздел [Устранение неполадок](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Публикация приложения
Теперь разверните свое приложение на сервере или в Azure и наблюдайте за тем, как накапливаются данные.

![Опубликуйте свое веб-приложение с помощью Visual Studio](./media/app-insights-windows-services/15-publish.png)

При работе в режиме отладки телеметрия передается через конвейер, поэтому данные должны появиться в течение нескольких секунд. При развертывании приложения в конфигурации выпуска данные накапливаются медленнее.

#### <a name="no-data-after-you-publish-to-your-server"></a>Отсутствуют данные после публикации на сервере?
Откройте следующие порты для исходящего трафика в брандмауэре сервера:

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### <a name="trouble-on-your-build-server"></a>Проблемы на сервере сборки?
Изучите [этот элемент устранения неполадок](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Если приложение генерирует много телеметрических данных (а вы используете пакет SDK ASP.NET версии 2.0.0-beta3 или выше), модуль адаптивной выборки автоматически сокращает объем отправляемых на портал данных, пересылая только репрезентативную часть событий. При этом связанные с тем же запросом события отбираются как группа, что позволяет перемещаться между связанными событиями. 
> [Дополнительная информация о выборке](app-insights-sampling.md).
> 
> 

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Дальнейшие действия
* [добавить дополнительную телеметрию](app-insights-asp-net-more.md) .


