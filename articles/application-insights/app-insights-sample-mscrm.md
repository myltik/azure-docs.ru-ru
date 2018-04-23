---
title: Microsoft Dynamics CRM и Azure Application Insights | Документация Майкрософт
description: Получение данных телеметрии из Microsoft Dynamics CRM Online с помощью Application Insights. Пошаговое руководство по настройке, получению данных, визуализации и экспорту.
services: application-insights
documentationcenter: ''
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: mbullwin
ms.openlocfilehash: ced4f62b4f27ede28c61e8d2670432ddd7b76f9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
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
3. Откройте страницу "Начало работы", а затем откройте "Мониторинг и диагностика приложения на стороне клиента".

    ![Фрагмент кода для вставки на веб-страницу](./media/app-insights-sample-mscrm/03.png)

**Не закрывайте страницу с кодом** , выполняя следующий шаг в другом окне браузера. Код вскоре вам понадобится. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Создание веб-ресурса JavaScript в Microsoft Dynamics CRM
1. Откройте экземпляр CRM Online и войдите с правами администратора.
2. Последовательно откройте элементы "Параметры Microsoft Dynamics CRM", "Настройка", "Настройка системы".

    ![Параметры Microsoft Dynamics CRM](./media/app-insights-sample-mscrm/00001.png)

    ![Параметры > Настройки](./media/app-insights-sample-mscrm/00002.png)

1. Создайте ресурс JavaScript.

    ![Диалоговое окно создания веб-ресурса](./media/app-insights-sample-mscrm/07.png)

    Присвойте ему имя, выберите тип **Script (JScript)** и откройте текстовый редактор.

    ![Откройте текстовый редактор](./media/app-insights-sample-mscrm/00004.png)
2. Скопируйте код из Application Insights. При копировании необходимо пропускать теги скриптов. См. ниже снимок экрана.

    ![Задайте ключ инструментирования](./media/app-insights-sample-mscrm/000005.png)

    Код содержит ключ инструментирования, который идентифицирует ресурс Application Insights.
3. Щелкните «Сохранить», а затем «Опубликовать».

    ![Сохранение и публикация](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Формы инструментов
1. В Microsoft CRM Online откройте форму «Учетная запись».

    ![Форма учетной записи](./media/app-insights-sample-mscrm/00007.png)
2. Откройте свойства формы.

    ![Свойства формы](./media/app-insights-sample-mscrm/00008.png)
3. Добавьте созданный веб-ресурс JavaScript.

    ![Меню "Добавить"](./media/app-insights-sample-mscrm/13.png)

4. Сохраните и опубликуйте настройки формы.

## <a name="metrics-captured"></a>Собранные показатели телеметрии
Теперь сбор данных телеметрии для формы настроен. При каждом использовании данные будут отправляться в ресурс Application Insights.

Ниже приведены примеры отображаемых данных.

#### <a name="application-health"></a>Работоспособность приложения
![Время загрузки образца страницы](./media/app-insights-sample-mscrm/15.png)

![Пример диаграммы просмотров страницы](./media/app-insights-sample-mscrm/16.png)

Исключения браузера:

![Диаграмма "Исключения браузера"](./media/app-insights-sample-mscrm/17.png)

Щелкните диаграмму, чтобы получить более подробную информацию.

![Список исключений](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Использование
![Представления числа пользователей, сеансов и просмотров страниц](./media/app-insights-sample-mscrm/19.png)

![Диаграммы сеансов](./media/app-insights-sample-mscrm/20.png)

![Версии браузеров](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Браузеры
![Разбивка времени загрузки страницы](./media/app-insights-sample-mscrm/22.png)

![Количество сеансов по версиям браузера](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Географическое положение
![Число сеансов по странам](./media/app-insights-sample-mscrm/24.png)

![Число сеансов и пользователей по странам](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Запрос на просмотр внутренней страницы
![Сводка по просмотрам страницы](./media/app-insights-sample-mscrm/26.png)

![Поиск событий просмотра страницы](./media/app-insights-sample-mscrm/27.png)

![Похожие просмотры страниц](./media/app-insights-sample-mscrm/28.png)

![Свойства просмотра страниц](./media/app-insights-sample-mscrm/29.png)

![Страницы по сеансам](./media/app-insights-sample-mscrm/30.png)

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
