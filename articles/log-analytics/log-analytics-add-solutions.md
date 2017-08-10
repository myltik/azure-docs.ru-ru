---
title: "Добавление решений для управления Azure Log Analytics | Документация Майкрософт"
description: "Решения для управления Operations Management Suite (OMS) и Log Analytics представляют собой коллекцию логики, визуализации и правил получения данных, предоставляющую метрики, связанные с определенной проблемной областью."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ee0191c63f12ae32708095362b8028a61ca731cc
ms.contentlocale: ru-ru
ms.lasthandoff: 08/04/2017

---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Добавление решений для управления Azure Log Analytics в рабочую область

Решения для управления Log Analytics представляют собой коллекцию **логики**, **визуализации** и **правил получения данных**, предоставляющую метрики, связанные с определенной проблемной областью. В этой статье перечислены решения для управления, поддерживаемые Log Analytics, и показано, как их можно добавлять и удалять в рабочей области с помощью портала Azure. Эти решения можно также добавить на портале OMS с помощью коллекции решений.

Решения для управления позволяют получить более подробные данные для:

* изучения и более быстрого решения проблем с работоспособностью;
* сбора и сопоставления различных типов машинных данных;
* заблаговременного выполнения операций, которые предоставляет решение.

> [!NOTE]
> Log Analytics включает в себя функции поиска по журналам, поэтому для активации решения для управления устанавливать его необязательно. Однако для получения визуализации данных, рекомендуемых условий поиска и аналитических следует добавить решения для управления в рабочую область.

В этой статье описывается, как добавить решения для управления в рабочую область с помощью портала Azure Marketplace. После добавления решения данные собираются с серверов в вашей инфраструктуре и отправляются в службу OMS. Обычно обработка в OMS занимает от нескольких минут до часа. Обработанные данные можно просмотреть в службе OMS.

Если решение для управления больше не нужно, его можно удалить. При удалении решения для управления его данные не отправляются в OMS. Если используется ценовая категория "Бесплатный", удаление решения может уменьшить объем используемых данных, что поможет не превысить ежедневную квоту данных.

## <a name="view-available-management-solutions"></a>Просмотр доступных решений для управления

В Azure Мarketplace доступен ряд [административных решений (решений для управления) для Log Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Эти решения для управления можно установить из Azure Мarketplace, щелкнув ссылку для **скачивания** в нижней части каждого решения.

## <a name="add-a-management-solution"></a>Добавление решения для управления
1. Войдите на [портал Azure](https://portal.azure.com), используя подписку Azure, если вы еще этого не сделали.
2. В колонке **Создать** выберите **Marketplace** > **Мониторинг и управление**.
3. В колонке **Мониторинг и управление** щелкните **See all** (Просмотреть все).  
    ![Колонка "Мониторинг и управление"](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. Справа от элемента **Решения для управления** щелкните **More** (Дополнительные).
5. В колонке **Решения для управления** выберите решение для управления, которое вы хотите добавить в рабочую область.  
    ![Колонка "Мониторинг и управление"](./media/log-analytics-add-solutions/management-solutions.png)  
6. В колонке решения для управления просмотрите сведения о решении, затем щелкните **Создать**.
7. В колонке *имя решения для управления* выберите рабочую область, в которую нужно добавить решение для управления.
8. При необходимости измените параметры подписки Azure, группы ресурсов и расположения для рабочей области. Можно также выбрать **параметры службы автоматизации**. Щелкните **Создать**.  
    ![Рабочая область решения](./media/log-analytics-add-solutions/solution-workspace.png)  
9. Чтобы начать использовать решение для управления, добавленное в рабочую область, выберите **Log Analytics** > **Подписки** > ***имя рабочей области*** > **Обзор**. Появится новый элемент для вашего решения для управления. Щелкните этот элемент, чтобы открыть его и начать использовать решение после сбора данных для него.

## <a name="remove-a-management-solution"></a>Удаление решения для управления

1. На [портале Azure](https://portal.azure.com) выберите **Log Analytics** > **Подписки** > ***имя рабочей области***, затем в колонке ***имя рабочей области*** щелкните **Решения**.
2. Из списка решений для управления выберите решение, которое требуется удалить.
3. В колонке решения для рабочей области щелкните **Удалить**.  
    ![Удаление решения](./media/log-analytics-add-solutions/solution-delete.png)  
4. В диалоговом окне подтверждения нажмите кнопку **Да**.

## <a name="offers-and-pricing-tiers"></a>Предложения и ценовые категории

В следующей таблице указаны решения для управления, относящиеся к каждому предложению Operations Management Suite.
В таблице также указаны ценовые категории, доступные для каждого для решения управления.
Все решения в приведенной таблице доступны на портале Azure и в коллекции решений на портале Log Analytics.

| Решение для управления                                                                       | ПРЕДЛОЖЕНИЕ                                                                     | Ценовые категории<sup>1</sup>                                                 | Примечания |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Анализ журнала действий](log-analytics-activity.md)                                                                   | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | 90 дней данные доступны бесплатно.<br>Для данных не действует ограничение уровня "Бесплатный". |
| [Оценка AD](log-analytics-ad-assessment.md)                                           | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Состояние репликации AD](log-analytics-ad-replication-status.md)                           | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | Добавление с портала Azure или Marketplace недоступно. |
| Работоспособность агента                                                                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | Для данных не действует ограничение уровня "Бесплатный".<br> Добавление с портала Azure или Marketplace недоступно. |
| [Управление оповещениями](log-analytics-solution-alert-management.md)                            | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | Добавление с портала Azure или Marketplace недоступно. |
| Соединитель Application Insights <br>(предварительная версия)                                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| Гибридная рабочая роль службы автоматизации                                                                  | <ul><li>Automation and Control</li></ul>                                  | Free<br> На&nbsp;узел&nbsp;(OMS)                                                                         | Требуется рабочая область Log Analytics, которую нужно привязать к учетной записи службы автоматизации. |
| [Анализ шлюзов приложений Azure](log-analytics-azure-networking-analytics.md)    | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Анализ групп безопасности сети Azure](log-analytics-azure-networking-analytics.md)     | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Службы анализа SQL Azure (предварительная версия)](log-analytics-azure-sql.md)                                                       | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br>На&nbsp;узел&nbsp;(OMS)                                                                          | Требуется рабочая область Log Analytics, которую нужно привязать к учетной записи службы автоматизации.|
| Архивация                                                                                    | <ul><li>Анализ и аналитика</li></ul>                                   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)                                                                       | Требуется классическое резервное хранилище.<br> Добавление с портала Azure или Marketplace недоступно. |
| Capacity and Performance <br>(предварительная версия)                                                    | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Отслеживание изменений](log-analytics-change-tracking.md)                                       | <ul><li>Automation and Control</li></ul>                                  | Free<br> На&nbsp;узел&nbsp;(OMS)                                                                         | Требуется рабочая область Log Analytics, которую нужно привязать к учетной записи службы автоматизации. |
| [Контейнеры](log-analytics-containers.md)                                                 | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Соединитель управления ИТ-службами](log-analytics-itsmc-overview.md)<br>(предварительная версия)                                               | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> На&nbsp;узел&nbsp;(OMS)     | |
| HDInsight HBase Monitoring <br>(предварительная версия)                                                  | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Анализ хранилища ключей](log-analytics-azure-key-vault.md)                   | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| Logic Apps B2B                    | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | Добавление с портала Azure или Marketplace недоступно. |
| [Оценка вредоносных программ](log-analytics-malware.md)                                            | <ul><li>Служба "Безопасность и соответствие требованиям"</li></ul>                                 | Free<br> Автономный<br>На&nbsp;узел&nbsp;(OMS)                                                                           | Если вы добавите решения для обеспечения безопасности и соответствия требованиям после 19 июня 2017 г., [плата будет начисляться за каждый узел](https://azure.microsoft.com/pricing/details/security-compliance/), независимо от ценовой категории рабочей области. В первые 60 дней плата не взимается.  |
| [Монитор производительности сети](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Анализ и аналитика</li></ul>                                   | Free<br> На&nbsp;узел&nbsp;(OMS)                                                                         | |
| Office 365 Analytics <br>(предварительная версия)                                                        | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Безопасность и аудит](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Security&nbsp;and&nbsp;Compliance</li></ul>                       | Free<br> Автономный<br>На&nbsp;узел&nbsp;(OMS)                                                                           | Это решения нужно для сбора журналов событий безопасности.<br>Если вы добавите решения для обеспечения безопасности и соответствия требованиям после 19 июня 2017 г., [плата будет начисляться за каждый узел](https://azure.microsoft.com/pricing/details/security-compliance/), независимо от ценовой категории рабочей области. В первые 60 дней плата не взимается. |
| [Анализ Service Fabric](log-analytics-service-fabric.md) <br>(предварительная версия)                 | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| [Схема услуги](../operations-management-suite/operations-management-suite-service-map.md) <br>(предварительная версия)| <ul><li>Анализ и аналитика</li></ul>                       | Free<br> На&nbsp;узел&nbsp;(OMS)                                                                         | Доступно в восточной части США, Западной Европе и западно-центральной части США.    |
| Site Recovery                                                                             | <ul><li>Анализ и аналитика</li></ul>                                   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)                                                                       | Требуется классическое хранилище Site Recovery.<br> Добавление с портала Azure или Marketplace недоступно. |
| [Оценка SQL](log-analytics-sql-assessment.md)                                         | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| Запуск и остановка виртуальных машин в нерабочее время<br>(предварительная версия)                                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> На&nbsp;узел&nbsp;(OMS)                                                                         | Требуется рабочая область Log Analytics, которую нужно привязать к учетной записи службы автоматизации. |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | Добавление с портала Azure или Marketplace недоступно. |
| [System Center Operations Manager Assessment](log-analytics-scom-assessment.md) <br>(предварительная версия) | <ul><li>Анализ и аналитика</li><li>Служба Log Analytics</li></ul>         | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| управление обновлениями;                                                                         | <ul><li>Automation and Control</li></ul>                                  | Free<br> На&nbsp;узел&nbsp;(OMS)                                                                         | Требуется рабочая область Log Analytics, которую нужно привязать к учетной записи службы автоматизации. |
| [Соответствие обновлений](https://technet.microsoft.com/en-us/itpro/windows/manage/update-compliance-get-started) <br>(предварительная версия)                                                             | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | Плата за данные или узлы не взимается.<br>Для данных не действует ограничение уровня "Бесплатный".<br> Добавление с портала Azure или Marketplace недоступно. |
| [Готовность к обновлению](https://technet.microsoft.com/itpro/windows/deploy/manage-windows-upgrades-with-upgrade-readiness)                                                            | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | Плата за данные или узлы не взимается.<br>Для данных не действует ограничение уровня "Бесплатный".<br> Добавление с портала Azure или Marketplace недоступно. |
| [VMware Monitoring](log-analytics-vmware.md) <br>(предварительная версия)                                | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> Стандартная<br> Премиум&nbsp;(OMS)<br> На&nbsp;ГБ&nbsp;(изолированное решение)<br> На&nbsp;узел&nbsp;(OMS)   | |
| Wire Data 2.0<br>(предварительная версия)                                                                | <ul><li>Анализ и аналитика</li></ul>                                   | Free<br> На&nbsp;узел&nbsp;(OMS)                                                                         | Доступно в восточной части США, Западной Европе и западно-центральной части США. |

<sup>1</sup> Ценовые категории *Стандартная* и *Премиум (OMS)* доступны только клиентам, которые создали свою область Log Analytics до 21 сентября 2016 года.

### <a name="community-provided-management-solutions"></a>Решения для управления от сообщества

Предоставляемые сообществом решения можно получить из [коллекции шаблонов Azure](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) и непосредственно у авторов.

| Решение для управления               | ПРЕДЛОЖЕНИЕ                                                                     | Ценовые категории                         | Примечания |
| ---                               | ---                                                                       | ---                                   | ---   |
| Все решения от сообщества  | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Служба Log Analytics</li></ul>   | Free<br> На&nbsp;узел&nbsp;(OMS)     |   Требуется рабочая область Log Analytics, которую нужно привязать к учетной записи службы автоматизации. |




## <a name="data-collection-details"></a>Сведения о сборе данных
В следующих таблицах приведены методы сбора данных и другие сведения о том, как собираются данные для решений для управления и источников данных Log Analytics. Эти таблицы разбиты по предлагаемым решениям, тождественным [ценовым категориям подписки](https://go.microsoft.com/fwlink/?linkid=827926). Решение для анализа журнала действий бесплатно для всех ценовых категорий.

Агент Log Analytics для Windows и агент System Center Operations Manager по сути одинаковы. Однако в агенте Windows предусмотрена дополнительная возможность, позволяющая ему подключиться к рабочей области OMS и выполнять маршрутизацию через прокси-сервер. Агент Operations Manager должен использоваться в качестве агента OMS для взаимодействия с OMS. Агенты Operations Manager в этой таблице являются агентами OMS, подключенными к Operations Manager. Сведения о подключении существующей среды Operations Manager к OMS см. в статье [Подключение Operations Manager к Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Тип используемого агента определяет способ отправки данных в OMS со следующими условиями:
> - Можно использовать агент Windows или агент OMS, подключенный к Operations Manager.
> - Когда требуется использовать Operations Manager, данные агента Operations Manager для решения всегда передаются в OMS с помощью группы управления Operations Manager. Кроме того, если требуется использовать Operations Manager, то в решении используется только агент Operations Manager.
> - Если использовать Operations Manager не обязательно и в таблице показано, что данные агента Operations Manager передаются в OMS с помощью группы управления, то передача этих данных в OMS будет всегда осуществляться с помощью групп управления. Агенты Windows обходят группу управления и передают свои данные непосредственно в OMS.
> - Если при передаче данных агента Operations Manager не используется группа управления, то данные отправляются непосредственно в OMS, минуя группу управления.

### <a name="insight--analytics--log-analytics"></a>Insight and Analytics и Log Analytics

| Решение для управления | Платформа | Microsoft Monitoring Agent | Агент Operations Manager | Хранилище Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Анализ журнала действий | Таблицы Azure | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | при уведомлении |
| Оценка AD |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 дней |
| Состояние репликации AD |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |5 дней |
| Работоспособность агента | Windows и Linux | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | 1 минута |
| Управление оповещениями (Nagios) |Linux |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |При получении |
| Управление оповещениями (Zabbix) |Linux |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |1 минута |
| Управление оповещениями (Operations Manager) |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |3 минуты |
| Соединитель Application Insights (предварительная версия) | Таблицы Azure | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | при уведомлении |
| Анализ шлюзов приложений Azure | Таблицы Azure | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | при уведомлении |
| Анализ групп безопасности сети Azure | Таблицы Azure | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | при уведомлении |
| Службы анализа SQL Azure (предварительная версия) |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 минут |
| Управление емкостью; |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |при получении |
| Контейнеры | Windows и Linux | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 минуты |
| анализ хранилища ключей. |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |при уведомлении |
| Монитор производительности сети | Windows | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | Подтверждения TCP выполняются каждые 5 секунд, данные отправляются каждые 3 минуты |
| Office 365 Analytics (предварительная версия) |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |при уведомлении |
| Анализ Service Fabric |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 мин |
| Схема услуги | Windows и Linux | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | 15 секунд |
| Оценка SQL |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 дней |
| SurfaceHub |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |При получении |
| Оценка System Center Operations Manager (предварительная версия) | Windows | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | 7 дней |
| Анализ обновлений (предварительная версия) | Windows | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | 2 дня |
| Мониторинг VMware (предварительная версия) | Linux | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 минуты |
| Проводные данные |Windows (2012 R2/8.1 или более поздней версии) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | 1 минута |


### <a name="automation--control"></a>Автоматизация и контроль

| Решение для управления | Платформа | Microsoft Monitoring Agent | Агент Operations Manager | Хранилище Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Гибридная рабочая роль службы автоматизации | Windows | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | Недоступно |
| Отслеживание изменений |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |ежечасно |
| Отслеживание изменений |Linux |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |ежечасно |
| управление обновлениями; | Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |не меньше 2 раз в день и через 15 минут после установки обновления |

### <a name="security--compliance"></a>Безопасность и соответствие требованиям

| Решение для управления | Платформа | Microsoft Monitoring Agent | Агент Operations Manager | Хранилище Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Оценка защиты от вредоносных программ |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |ежечасно |
| Безопасность и аудит <sup>1</sup> | Windows и Linux | ![Некоторые](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Некоторые](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Некоторые](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Некоторые](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | Различная |

<sup>1</sup> Решение для защиты и аудита может собирать журналы от агентов Windows, Linux и Operations Manager. Сведения о сборе данных приведены в разделе [Источники данных](#data-sources).

- syslog
- Журналы событий безопасности Windows
- Журналы брандмауэра Windows
- Журналы событий Windows



### <a name="protection--recovery"></a>Защита и восстановление

| Решение для управления | Платформа | Microsoft Monitoring Agent | Агент Operations Manager | Хранилище Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Архивация | Таблицы Azure | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | Недоступно |
| Azure Site Recovery | Таблицы Azure | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) | Недоступно |


### <a name="data-sources"></a>Источники данных


| Источник данных | Платформа | Microsoft Monitoring Agent | Агент Operations Manager | Хранилище Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- | --- |
| журналы действий Azure; |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |при уведомлении |
| Журналы диагностики Azure |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |при уведомлении |
| Диагностические метрики Azure |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |при уведомлении |
| Трассировка событий Windows |Windows |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 мин |
| Журналы IIS |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 мин |
| Счетчики производительности |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |по расписанию, не менее 10 секунд |
| Счетчики производительности |Linux |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |по расписанию, не менее 10 секунд |
| syslog |Linux |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |Из хранилища Azure — 10 минут, из агента — при получении |
| Журналы событий безопасности Windows |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |для хранилища Azure — 10 мин, для агента — при получении |
| Журналы брандмауэра Windows |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |при получении |
| Журналы событий Windows |Windows |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png) |![Да](./media/log-analytics-add-solutions/oms-bullet-green.png) |для хранилища Azure — 10 мин, для агента — при получении |



## <a name="preview-management-solutions-and-features"></a>Предварительные версии решений для управления и компонентов
Запуск службы и выполнение процедур разработки позволяет сотрудничать с клиентами в процессе разработки функций и решений.

На этапе закрытой предварительной версии мы предоставляем небольшой группе клиентов доступ к ранней реализации функции или решения, чтобы получить обратную связь и внести усовершенствования. Ранняя реализация включает минимум функций и операционных возможностей.

Наша цель — быстро опробовать новые функции и понять, что работает, а что нет. Мы работаем таким образом до тех пор, пока отзывы пользователей закрытой предварительной версии не покажут, что мы готовы к выпуску общедоступной предварительной версии.

В общедоступной предварительной версии функция или решение становятся доступными для всех пользователей, что позволяет получить больше отзывов и проверить нашу оценку и эффективность. На этом этапе

* Предварительные версии компонентов появляются на вкладке "Параметры" и могут быть активированы любым пользователем.
* Предварительные версии решений можно добавлять из коллекции или с помощью сценария.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Что нужно знать о предварительных версиях компонентов и решений?
Мы всегда с радостью выпускаем новые функции и решения для управления и нам нравится сотрудничать с вами в процессе их разработки.

Предварительные версии компонентов и решений подходят не для всех. Прежде чем подавать запрос об участии в тестировании закрытой предварительной версии или активировать общедоступную предварительную версию, убедитесь, что вы сможете работать с продуктом на этапе разработки.

Активировав предварительную версию функции через портал, вы увидите предупреждение о том, что эта функция находится на этапе предварительной версии.

#### <a name="for-both-private-and-public-preview"></a>Для *закрытых* и *общедоступных* предварительных версий
И к закрытой, и к общедоступной предварительным версиям применимо следующее.

* Они могут работать некорректно.
  * Проблемы могут быть разными, от незначительных раздражающих факторов до абсолютно неработающих функций.
* Предварительная версия может негативно повлиять на ваши системы или среду.
  * Мы стараемся избежать негативного влияния на системы, используемые с OMS, но иногда происходит непредвиденное.
* Возможна потеря или повреждение данных.
* Мы можем запрашивать у вас журналы диагностики или другие данные для устранения проблем.
* Компонент или решение можно удалить (временно или навсегда).
  * В зависимости от результатов работы на этапе предварительной версии мы можем отказаться от выпуска функции или решения.
* Предварительные версии могут не работать или тестироваться со всеми конфигурациями; кроме того, мы можем ограничить следующее.
  * Операционные системы, которые можно использовать (например, на этапе предварительной версии компонент может применяться только в Linux).
  * Тип агента (MMA, Operations Manager), который можно использовать (например, функция может не работать с Operations Manager на этапе предварительной версии).  
* Предварительные версии решений и компонентов не регулируются соглашением об уровне обслуживания.
* Работа с предварительными версиями компонентов потребует платы за использование.
* Компоненты или возможности, необходимые для эффективной работы компонента или решения, могут отсутствовать или быть неполными.
* Компоненты и решения могут быть доступны не во всех регионах.
* Компоненты и решения могут быть не локализованы.
* Компоненты и решения могут иметь ограничение по числу клиентов или устройств, которые могут их использовать.
* Для настройки конфигурации и включения решения или компонента могут потребоваться сценарии.
* Пользовательский интерфейс может быть неполным и периодически меняться.
* Общедоступные предварительные версии могут не подойти для ваших рабочих или критически важных систем.

#### <a name="for-private-preview"></a>Для *закрытых* предварительных версий
Помимо вышеизложенного, к закрытым предварительным версиям применимо следующее.

* Мы рассчитываем на обратную связь с вашей стороны — это поможет нам усовершенствовать компонент или решение.
* Мы можем обращаться к вам за отзывами с помощью опросов, по телефону и по электронной почте.
* Предварительные версии могут работать некорректно.
* Для участия может потребоваться заключить соглашение о неразглашении, так как предварительные версии могут включать в себя конфиденциальное содержимое.
  * Прежде чем писать в блог или Twitter либо сообщать что-либо третьим сторонам, уточните у руководителя программы, ответственного за предварительную версию, какие ограничения связаны с разглашением информации.
* Предварительные версии не стоит использовать в рабочих и критически важных системах.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Как получить доступ к закрытым предварительным версиям функций и решений?
Мы приглашаем клиентов участвовать в тестировании предварительных версий различными способами в зависимости от конкретной функции или решения.

* Участвуя в ежемесячных опросах клиентов и предоставив нам разрешение на дальнейшую связь, вы повышаете свои шансы на участие в тестировании.
* Ваше участие в тестировании может быть предложено менеджером по работе с клиентами Майкрософт.
* Данные о регистрации опубликованы в Twitter [msopsmgmt](https://twitter.com/msopsmgmt)
* Данные о регистрации распространяются на общественных мероприятиях — ищите нас на собраниях, конференциях и в онлайн-сообществах.

## <a name="next-steps"></a>Дальнейшие действия
* Выполните [поиск по журналам](log-analytics-log-searches.md) для просмотра подробных сведений, собранных решениями для управления.

