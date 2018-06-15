---
title: Заметки о выпуске для Application Insights | Документация Майкрософт
description: Добавление маркеров развертывания или сборки для диаграмм обозревателя метрик в Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: fb4bcd57062017c0d7ee802ba1f46660476f6af2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293441"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Заметки к диаграммам метрик в Application Insights
Заметки к диаграммам [обозревателя метрик](app-insights-metrics-explorer.md) показывают, где развернута новая сборка, а также отображают другие важные события. С их помощью легко увидеть, повлияли ли ваши изменения на производительность приложения. Заметки могут быть созданы автоматически [системой сборки Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). Заметки можно также создавать, чтобы помечать какие-либо события, [используя PowerShell](#create-annotations-from-powershell).

![Пример заметок с видимой корреляцией с временем ответа сервера](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Заметки о выпуске сборки VSTS

Заметки к выпуску являются функцией службы облачной сборки и выпуска Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Установка расширения заметок (однократно)
Чтобы получить возможность создания заметок к выпуску, необходимо установить одно из расширений Team Service, доступных в магазине Visual Studio.

1. Войдите в свой проект в [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online).
2. В магазине Visual Studio [найдите расширение заметок к выпуску](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)и добавьте его к своей учетной записи Team Services.

![В верхнем правом углу веб-страницы Team Services откройте "Магазин". Выберите Visual Team Services и нажмите кнопку "Дополнительно" в разделе "Сборка и выпуск".](./media/app-insights-annotations/10.png)

Это необходимо сделать только один раз для учетной записи Visual Studio Team Services. Теперь можно настроить заметки к выпуску для любого проекта в вашей учетной записи. 

### <a name="configure-release-annotations"></a>Настройка заметок к выпуску

Для каждого шаблона выпуска VSTS необходимо получить отдельный ключ API.

1. Выполните вход на [портал Microsoft Azure](https://portal.azure.com) и откройте ресурс Application Insights, который используется для мониторинга вашего приложения. (Или [создайте новый](app-insights-overview.md), если вы этого еще не сделали.)
2. Откройте **Доступ через API** и выберите **Идентификатор Application Insights**.
   
    ![На сайте portal.azure.com откройте ресурс Application Insights и выберите "Параметры". Откройте "Доступ к API". Скопируйте идентификатор приложения.](./media/app-insights-annotations/20.png)

4. В отдельном окне браузера откройте (или создайте) шаблон выпуска, который управляет развертываниями из Visual Studio Team Services. 
   
    Добавьте задачу и выберите в меню задачу заметок к выпуску Application Insights.
   
    Вставьте **идентификатор приложения** , скопированный из колонки "Доступ к API".
   
    ![В Visual Studio Team Services откройте "Выпуск", выберите определение выпуска и нажмите кнопку "Изменить". Щелкните "Добавьте задачу" и выберите "Заметки к выпуску Application Insights". Вставьте ИД Application Insights.](./media/app-insights-annotations/30.png)
4. Задайте в качестве значения поля **APIKey** переменную `$(ApiKey)`.

5. В окне Azure создайте ключ API и скопируйте его.
   
    ![В колонке "Доступ к API" в окне Azure щелкните "Создать ключ API". Введите комментарий, щелкните "Написать заметки" и нажмите кнопку "Создать ключ". Скопируйте новый ключ.](./media/app-insights-annotations/40.png)

6. Перейдите на вкладку "Конфигурация" шаблона выпуска.
   
    Создайте определение переменной для `ApiKey`.
   
    Вставьте ключ API в определение переменной ApiKey.
   
    ![В окне Team Services перейдите на вкладку "Конфигурация" и щелкните "Добавить переменную". Задайте "ApiKey" в качестве имени и в поле "Значение" вставьте ключ, который был только что создан, и щелкните значок замка.](./media/app-insights-annotations/50.png)
7. Наконец, **сохраните** определение выпуска.


## <a name="view-annotations"></a>Просмотр заметок
Теперь при каждом развертывании нового выпуска с помощью шаблона выпуска заметки будут отправляться в Application Insights. Заметки будут отображаться на диаграммах в обозревателе метрик.

Щелкните любой маркер заметки, чтобы открыть подробные сведения о выпуске, включая запросившую сторону, ветвь системы управления версиями, определение выпуска, среду и многое другое.

![Щелкните любой маркер заметки о выпуске.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Создание настраиваемых заметок в PowerShell
Вы можете создать аннотации из любого процесса на свой выбор (без использования Visual Studio Team System). 


1. Создайте локальную копию [сценария PowerShell из GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Получите идентификатор приложения и создайте ключ API в колонке "Доступ через API".

3. Вызовите сценарий следующим образом.

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Сценарий легко изменить, например, чтобы создать заметки для предыдущих выпусков.

## <a name="next-steps"></a>Дополнительная информация

* [Создание рабочих элементов](app-insights-diagnostic-search.md#create-work-item)
* [Автоматизация с помощью PowerShell](app-insights-powershell.md)
