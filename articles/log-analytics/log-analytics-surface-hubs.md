---
title: Мониторинг устройств Surface Hub с помощью Azure Log Analytics | Документация Майкрософт
description: Решение Surface Hub позволяет отслеживать работоспособность устройств Surface Hub и понимать, как они используются.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 525b3ced979834a956f91ef8c6f647b659ca21f1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2018
ms.locfileid: "29179231"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Мониторинг работоспособности устройств Surface Hub с помощью Log Analytics

![Символ решения Surface Hub](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

В этой статье описывается использование решения Surface Hub в службе Log Analytics для мониторинга устройств Microsoft Surface Hub. Log Analytics позволяет отслеживать работоспособность устройств Surface Hub и понимать, как они используются.

На каждом устройстве Surface Hub установлен агент Microsoft Monitoring Agent. Именно через этот агент можно отправлять данные с устройства Surface Hub в Log Analytics. Файлы журнала считываются с устройств Surface Hub и затем отправляются в Log Analytics. Проблемы, такие как отключение серверов от сети, отсутствие синхронизации календаря или невозможность войти в Skype с использованием учетной записи устройства, отображаются в Log Analytics на панели мониторинга Surface Hub. С помощью данных на панели мониторинга можно определить устройства, которые не работают или столкнулись с другими проблемами, и, возможно, применить исправления обнаруженных проблем.

## <a name="install-and-configure-the-solution"></a>Установка и настройка решения
Для установки и настройки решений используйте указанные ниже данные. Для управления устройствами Surface Hub в Log Analytics потребуется следующее.

* Уровень [подписки Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/), поддерживающий количество устройств, которые требуется отслеживать. Цены на Log Analytics зависят от количества зарегистрированных устройств и объема обрабатываемых данных. Это необходимо учитывать при планировании развертывания Surface Hub.

Затем следует добавить существующую рабочую область Log Analytics или создать новую. Подробные инструкции по использованию любого из этих методов содержатся в статье [Начало работы с Log Analytics](log-analytics-get-started.md). После настройки рабочей области Log Analytics регистрировать устройства Surface Hub можно двумя способами:

* автоматически с помощью InTune;
* вручную в приложении **Settings** на устройстве Surface Hub.

## <a name="set-up-monitoring"></a>Настройка мониторинга
Работоспособность и активность устройств Surface Hub можно отслеживать с помощью Log Analytics. Устройство Surface Hub можно зарегистрировать с помощью Intune или локально, с помощью **параметров** устройства Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Подключение устройств Surface Hub к Log Analytics через Intune
Вам потребуется идентификатор и ключ для рабочей области Log Analytics, которая будет управлять устройствами Surface Hub. Их можно получить на портале Azure в разделе параметров рабочей области.

Intune — это продукт Microsoft, который позволяет централизованно управлять параметрами конфигурации Log Analytics, применяемыми к одному или нескольким устройствам. Чтобы настроить устройства через Intune, выполните следующие действия:

1. Войдите в Intune.
2. Перейдите в раздел **Settings (Параметры)** > **Connected Sources (Подключенные источники)**.
3. Создайте или отредактируйте политику на основе шаблона Surface Hub.
4. Перейдите к разделу OMS (оперативной аналитики Azure) политики и добавьте *идентификатор рабочей области* и *ключ рабочей области* Log Analytics в политику.
5. Сохраните политику.
6. Свяжите политику с соответствующей группой устройств.

   ![Политика Intune](./media/log-analytics-surface-hubs/intune.png)

Затем Intune синхронизирует параметры Log Analytics с устройствами в целевой группе, регистрируя их в рабочей области Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Подключение устройств Surface Hub к Log Analytics с помощью приложения Settings
Вам потребуется идентификатор и ключ для рабочей области Log Analytics, которая будет управлять устройствами Surface Hub. Их можно получить в разделе параметров рабочей области Log Analytics на портале Azure.

Если для управления средой не используется Intune, вы можете зарегистрировать устройства вручную в **параметрах** каждого устройства Surface Hub:

1. На устройстве Surface Hub откройте приложение **Settings**.
2. Введите учетные данные администратора устройства при появлении запроса.
3. Щелкните **This device** (Это устройство), затем в разделе **Monitoring** (Мониторинг) нажмите кнопку **Configure OMS Settings** (Настроить параметры OMS).
4. Выберите **Enable monitoring** (Включить мониторинг).
5. В диалоговом окне параметров OMS введите **идентификатор рабочей области** и **ключ рабочей области**.  
   ![параметры](./media/log-analytics-surface-hubs/settings.png)
6. Нажмите кнопку **ОК**, чтобы завершить настройку.

Появится подтверждение с сообщением о том, удалось ли применить конфигурацию к устройству. Если да, появится сообщение о том, что агент успешно подключен к Log Analytics. Затем устройство начнет отправку данных в Log Analytics, где эти данные можно просматривать и обрабатывать.

## <a name="monitor-surface-hubs"></a>Мониторинг устройств Surface Hub
Мониторинг устройств Surface Hub с помощью Log Analytics во многом напоминает мониторинг любых других зарегистрированных устройств.

1. Войдите на портал Azure.
2. Перейдите в рабочую область Log Analytics и выберите **Обзор**.
2. Щелкните элемент "Surface Hub".
3. Отобразится состояние работоспособности устройства.

   ![Панель мониторинга Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Можно создавать [оповещения](log-analytics-alerts.md) на основе существующих или настраиваемых поисковых запросов к журналу. Используя данные, собираемые службой Log Analytics с устройств Surface Hubs, можно выполнять поиск проблем и получать оповещения, которые отправляются при выполнении условий, заданных вами для устройств.

## <a name="next-steps"></a>Дополнительная информация
* Используйте [Поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных о Surface Hub.
* Создайте [оповещения](log-analytics-alerts.md), которые будут уведомлять вас о возникновении проблем с устройствами Surface Hub.
