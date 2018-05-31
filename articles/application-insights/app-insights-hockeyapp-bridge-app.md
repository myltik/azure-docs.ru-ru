---
title: Просмотр данных HockeyApp в Azure Application Insights | Документация Майкрософт
description: Анализ использования и производительности приложения Azure с помощью Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: cd185d799be5051340c2bfea44a1d1e69a1eb002
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32309850"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Просмотр данных HockeyApp в Application Insights

> [!NOTE]
> HockeyApp больше недоступно для новых приложений. Имеющиеся развертывания HockeyApp будут продолжать работать. Теперь рекомендуемой службой корпорации Майкрософт для мониторинга новых мобильных приложений является Visual Studio App Center. [Узнайте, как настраивать приложения с помощью App Center и Application Insights](app-insights-mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) — это служба мониторинга живых классических и мобильных приложений. В HockeyApp можно не только получать данные о сбоях, но и отправлять данные пользовательской телеметрии и телеметрии трассировки, чтобы отслеживать показатели использования и выполнять диагностику. Этот поток телеметрии можно запрашивать с помощью мощной функции [аналитики](app-insights-analytics.md) в [Azure Application Insights](app-insights-overview.md). Кроме того, вы можете [экспортировать данные пользовательской телеметрии и телеметрии трассировки](app-insights-export-telemetry.md). Чтобы включить эти функции, следует настроить мост (связующее приложение), который ретранслирует пользовательские данные HockeyApp в Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>Связующее приложение HockeyApp
Связующее приложение HockeyApp — это основной компонент, предоставляющий доступ к пользовательским данным и телеметрии трассировки HockeyApp в Application Insights с помощью функций анализа и непрерывного экспорта. Пользовательские события и события трассировки, собранные HockeyApp после создания связующего приложения HockeyApp, доступны в этих компонентах. Давайте посмотрим, как настроить такое связующее приложение.

В HockeyApp откройте параметры учетной записи и щелкните [API Tokens](https://rink.hockeyapp.net/manage/auth_tokens)(Маркеры API). Создайте новый маркер или воспользуйтесь существующим. Как минимум нужны права только для чтения. Сделайте копию маркера API.

![Получение маркера API HockeyApp](./media/app-insights-hockeyapp-bridge-app/01.png)

Перейдите на портал Azure и [создайте ресурс Application Insights](app-insights-create-new-resource.md). В качестве типа приложения укажите "HockeyApp bridge application" (Связующее приложение HockeyApp).

![Новый ресурс Application Insights](./media/app-insights-hockeyapp-bridge-app/02.png)

Имя указывать не нужно, так как оно автоматически присваивается на основе имени HockeyApp.

Откроется колонка с полями связующего приложения HockeyApp. 

![Мост: заполнение полей](./media/app-insights-hockeyapp-bridge-app/03.png)

Введите маркер HockeyApp, записанный ранее. Это действие добавляет в раскрывающееся меню "Приложение HockeyApp" все ваши приложения HockeyApp. Выберите нужное приложение и заполните оставшиеся поля. 

Откройте новый ресурс. 

Учтите, что запуск потока данных занимает некоторое время.

![Ресурс Application Insights: ожидание данных](./media/app-insights-hockeyapp-bridge-app/04.png)

Это все! Пользовательские данные и данные трассировки, собранные в инструментированном приложении HockeyApp, теперь доступны в компонентах анализа и непрерывного экспорта Application Insights.

Рассмотрим эти доступные компоненты подробнее.

## <a name="analytics"></a>Аналитика
Аналитика — это мощное средство для создания нерегламентированных запросов к данным, которое позволяет выполнять диагностику и анализ данных телеметрии, а также быстро находить основные причины и характерные признаки проблем.

![Аналитика](./media/app-insights-hockeyapp-bridge-app/05.png)

* [Знакомство с аналитикой в Application Insights](app-insights-analytics-tour.md)

## <a name="continuous-export"></a>непрерывный экспорт.
Непрерывный экспорт позволяет экспортировать данные в контейнер хранилища BLOB-объектов Azure. Это очень полезно, если вам нужно хранить данные дольше, чем позволяет срок хранения, доступный в службе Application Insights. Вы можете хранить данные в хранилище BLOB-объектов, обрабатывать их в базе данных SQL или использовать другое удобное решение для хранения данных.

[Экспорт данных телеметрии из Application Insights](app-insights-export-telemetry.md)

## <a name="next-steps"></a>Дополнительная информация
* [Знакомство с аналитикой в Application Insights](app-insights-analytics-tour.md)

