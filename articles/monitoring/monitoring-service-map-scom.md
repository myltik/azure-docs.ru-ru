---
title: Интеграция схемы услуги с System Center Operations Manager | Документация Майкрософт
description: "\"Сопоставление служб\" — это решение Azure, которое автоматически обнаруживает компоненты приложений в системах Windows и Linux и сопоставляет взаимодействие между службами. В этой статье описывается использование схемы услуги для автоматического создания схем распределенных приложений в Operations Manager."
services: monitoring
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: 6fbc49584b040f952fdff147207864d2d1f6377e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887444"
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Интеграция схемы услуги с System Center Operations Manager
  > [!NOTE]
  > Эта функция предоставляется в общедоступной предварительной версии.
  > 
  
Служба схемы услуги автоматически обнаруживает компоненты приложений в системах Windows и Linux и сопоставляет взаимодействие между службами. Схема услуги позволяет рассматривать серверы как взаимосвязанные системы, предоставляющие важные услуги. Схема услуги отображает сведения о подключениях между серверами, процессами и портами в любой подключенной по протоколу TCP архитектуре без дополнительной настройки. Пользователям требуется только установить агент. Дополнительные сведения см. в [документации по схеме услуги]( monitoring-service-map.md).

С помощью этой интеграции схемы услуги и System Center Operations Manager можно автоматически создавать схемы распределенных приложений в Operations Manager, которые основаны на динамических сопоставлениях зависимостей в схеме услуги.

## <a name="prerequisites"></a>предварительным требованиям
* Группа управления Operations Manager 2012 R2 или более поздней версии, управляющая набором серверов.
* Рабочая область Log Analytics с включенным решением "Сопоставление служб".
* Набор серверов (по крайней мере один), которые находятся под управлением Operations Manager и отправляют данные в схему услуги. Поддерживаются серверы Windows и Linux.
* Субъект-служба с доступом к подписке Azure, связанной с рабочей областью Log Analytics. Дополнительные сведения см. в руководстве по [созданию субъекта-службы](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Установка пакета управления схемы услуги
Интеграция Operations Manager и схемы услуг обеспечивается путем импорта набора пакетов управления Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Скачать пакет управления можно в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=55763). Этот набор содержит следующие пакеты управления:
* Microsoft.ServiceMap.Application.Views;
* Microsoft.System.Center.ServiceMap.Internal;
* Microsoft.System.Center.ServiceMap.Overrides;
* Microsoft.System.Center.ServiceMap.

## <a name="configure-the-service-map-integration"></a>Настройка интеграции схемы услуги
После установки пакета управления схемы услуги в области **Администрирование** в разделе **Operations Management Suite** отобразится новый узел — **Схема услуги**. 

Чтобы настроить интеграцию схемы услуги, выполните следующие действия:

1. Чтобы открыть мастер настройки, щелкните **Добавить рабочую область** в области **Service Map Overview** (Обзор схемы услуги).  

    ![Область "Service Map Overview" (Обзор схемы услуги)](media/monitoring-service-map/scom-configuration.png)

2. В окне **Настройка подключения** введите имя или идентификатор клиента, идентификатор приложения (имя пользователя или clientID) и пароль субъекта-службы, а затем нажмите кнопку **Далее**. Дополнительные сведения см. в руководстве по [созданию субъекта-службы](#creating-a-service-principal).

    ![Окно "Настройка подключения"](media/monitoring-service-map/scom-config-spn.png)

3. В окне **Subscription Selection** (Выбор подписки) выберите подписку Azure, группу ресурсов Azure (которая содержит рабочую область Log Analytics) и саму рабочую область Log Analytics, а затем нажмите кнопку **Далее**.

    ![Настройка Operations Manager: рабочая область](media/monitoring-service-map/scom-config-workspace.png)

4. В окне **Machine Group Selection** (Выбор группы компьютеров) выберите группы компьютеров решения "Сопоставление служб", которые необходимо синхронизировать с Operations Manager. Нажмите кнопку **Add/Remove Machine Groups** (Добавить или удалить группы компьютеров), выберите группы из списка **доступных групп компьютеров**и нажмите кнопку **Добавить**.  Выбрав группы, нажмите кнопку **ОК** для завершения.
    
    ![Настройка групп компьютеров в Operations Manager](media/monitoring-service-map/scom-config-machine-groups.png)
    
5. В окне **Выбор сервера** настройте группу серверов схемы услуги для синхронизации между Operations Manager и схемой услуги. Нажмите кнопку **Добавление и удаление серверов**.   
    
    Для интеграции и успешного построения схемы распределенного приложения для сервера он должен:

    * находиться под управлением Operations Manager;
    * находиться под управлением решения "Сопоставление служб";
    * входить в группу серверов решения "Сопоставление служб".

    ![Настройка Operations Manager: группа](media/monitoring-service-map/scom-config-group.png)

6. Необязательно. Выберите пул ресурсов сервера управления для взаимодействия с Log Analytics и щелкните **Add Workspace** (Добавить рабочую область).

    ![Настройка Operations Manager: пул ресурсов](media/monitoring-service-map/scom-config-pool.png)

    Для настройки и регистрации рабочей области Log Analytics может потребоваться около минуты. После ее настройки Operations Manager инициирует первую синхронизацию сопоставления служб.

    ![Настройка Operations Manager: пул ресурсов](media/monitoring-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Мониторинг схемы услуги
После подключения рабочей области Log Analytics в области **Мониторинг** консоли Operations Manager отобразится новая папка — "Сопоставление служб".

![Operations Manager: область "Мониторинг"](media/monitoring-service-map/scom-monitoring.png)

Папка "Сопоставление служб" имеет четыре узла:
* **Активные оповещения:** список всех активных оповещений об обмене данными между Operations Manager и решением "Сопоставление служб".  Обратите внимание, что оповещения Log Analytics не отображаются в Operations Manager. 

* **Серверы**: список отслеживаемых серверов, настроенных для синхронизации из схемы услуги.

    ![Operations Manager: область "Серверы"](media/monitoring-service-map/scom-monitoring-servers.png)

* **Machine Group Dependency Views** (Представления зависимостей групп компьютеров): здесь указаны группы компьютеров, синхронизируемых с решением "Сопоставление служб". Вы можете щелкнуть любую группу, чтобы просмотреть распределенные диаграммы приложения.

    ![Схема распределенного приложения Operations Manager](media/monitoring-service-map/scom-group-dad.png)

* **Server Dependency Views** (Представления зависимости серверов): список всех серверов, синхронизируемых из схемы услуги. Вы можете щелкнуть любой сервер, чтобы просмотреть его схему распределенного приложения.

    ![Схема распределенного приложения Operations Manager](media/monitoring-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Изменение или удаление рабочей области
Вы можете изменить или удалить настроенную рабочую область с помощью области **Service Map Overview** (Обзор схемы услуги) (область **Администрирование** > **Operations Management Suite** > **Схема услуги**). Пока что можно настроить только одну рабочую область Log Analytics.

![Operations Manager: область изменения рабочей области](media/monitoring-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Настройка правил и переопределений
Правило _Microsoft.SystemCenter.ServiceMapImport.Rule_ создается для периодического извлечения сведений из схемы услуги. Чтобы изменить время синхронизации, можно настроить переопределения правила (область **Разработка** > **Правила** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Operations Manager: окно свойств переопределений](media/monitoring-service-map/scom-overrides.png)

* **Enabled**: позволяет включить или отключить автоматическое обновление. 
* **IntervalMinutes**: позволяет сбросить настройки времени между обновлениями. Значение по умолчанию — один час. Если необходимо чаще синхронизировать карты серверов, можно изменить это значение.
* **TimeoutSeconds**: позволяет сбросить настройки времени ожидания запроса. 
* **TimeWindowMinutes**: позволяет сбросить настройки временного окна для запроса данных. Значение по умолчанию — 60 минут. Максимальное значение, разрешенное схемой услуги, — 60 минут.

## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения

В текущей версии существуют следующие проблемы и ограничения:
* Можно подключиться только к одной рабочей области Log Analytics.
* Хотя пользователи могут вручную добавить серверы в группу серверов решения "Сопоставление служб" с помощью области **Разработка**, сопоставления для этих серверов синхронизируются не сразу.  Они будут синхронизированы из решения "Сопоставление служб" во время следующего цикла синхронизации.
* Если внести изменения в схемы распределенного приложения, созданные в пакете управления, скорее всего, эти изменения будут перезаписаны при следующей синхронизации с решением "Сопоставление служб".

## <a name="create-a-service-principal"></a>Создание субъекта-службы
Официальная документация Azure по созданию субъекта-службы представлена в следующих статьях:
* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Отзыв
У вас есть комментарии относительно схемы услуги или этой документации? Посетите [страницу пользовательских мнений](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), где можно предложить функции или проголосовать за существующие предложения.
