---
title: 'Решение '
оценка: ''
конфигурации": ''
в: ''
log: ''
analytics: ''
'|': ''
microsoft: ''
azure": ''
description: 'Решение '
службе: ''
предоставляет: ''
подробные: ''
сведения: ''
о: ''
текущем: ''
состоянии: ''
инфраструктуры: ''
серверов: ''
system: ''
center: ''
operations: ''
manager: jwhit
при: ''
использовании: ''
агентов: ''
или: ''
группы: ''
управления: ''
manager.": ''
services: log-analytics
documentationcenter: ''
author: bandersmsft
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="configuration-assessment-solution-in-log-analytics"></a>Решение "Оценка конфигурации" в Log Analytics
Решение "Оценка конфигурации" в службе Log Analytics помогает обнаружить потенциальные проблемы конфигурации сервера с помощью оповещений и рекомендаций в базе знаний.

Для работы этого решения требуется System Center Operations Manager. Решение "Оценка конфигурации" недоступно, если используются только непосредственно подключенные агенты.

Для просмотра некоторых сведений в решении "Оценка конфигурации" требуется подключаемый модуль Silverlight для веб-браузера.

> [!NOTE]
> Начиная с 5 июля 2016 года решение "Оценка конфигурации" больше не будет добавляться к рабочим областям службы Log Analytics, а с 1 августа 2016 года у существующих пользователей больше не будет доступа к этому решению. Клиентам, использующим это решение для SQL Server или Active Directory, рекомендуется заменить его решением [Оценка SQL Server](log-analytics-sql-assessment.md), [Оценка Active Directory](log-analytics-ad-assessment.md) или [решением для контроля состояния репликации Active Directory ](log-analytics-ad-replication-status.md). Клиентам, использующим решение "Оценка конфигурации" для Windows, Hyper-V и System Center Virtual Machine Manager, для целостного рассмотрения проблем в среде рекомендуется использовать функциональные возможности по сбору событий и отслеживанию изменений.
> 
> 

![Элемент "Оценка конфигурации"](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Данные конфигурации собираются из отслеживаемых серверов и затем отправляются в службу OMS в облаке для обработки. К полученным данным применяется логика и облачная служба записывает данные. Обработанные данные для серверов отображаются в следующих областях.

* **Оповещения.** Отображаются относящиеся к конфигурации упреждающие оповещения, которые были созданы для наблюдаемых серверов. Их создают правила, разработанные организацией поддержки пользователей Майкрософт (CSS) на основе рекомендаций с мест.
* **Рекомендации в базе знаний**. Отображаются статьи базы знаний Майкрософт, которые рекомендуются для рабочих нагрузок в вашей инфраструктуре. Они автоматически предлагаются службой машинного обучения, исходя из вашей конфигурации.
* **Анализируемые серверы и рабочие нагрузки**. Отображаются серверы и рабочие нагрузки, которые отслеживает OMS.

![Панель мониторинга "Оценка конфигурации"](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### <a name="technologies-you-can-analyze-with-configuration-assessment"></a>Технологии, которые можно анализировать с помощью решения "Оценка конфигурации"
Решение "Оценка конфигурации" анализирует следующие рабочие нагрузки.

* Windows Server 2012 и Microsoft Hyper-V Server 2012
* Windows Server 2008 R2, Windows Server 2008 и Windows Server 2012 R2.
  * Active Directory
  * Узел Hyper-V
  * Общая операционная система
* SQL Server 2008 и более поздних версий
  * SQL Server Database Engine
* Microsoft SharePoint 2010
* Microsoft Exchange Server 2010 и Microsoft Exchange Server 2013
* Microsoft Lync Server 2013 и Lync Server 2010
* System Center 2012 SP1 — Virtual Machine Manager

Для SQL Server поддерживается анализ следующих 32-разрядных и 64-разрядных выпусков:

* SQL Server 2016 — все выпуски
* SQL Server 2014 — все выпуски
* SQL Server 2008 и 2008 R2 — все выпуски

Во всех поддерживаемых выпусках анализируется компонент SQL Server Database Engine. Кроме того, 32-разрядный выпуск SQL Server поддерживается при работе в режиме реализации WOW64.

## <a name="installing-and-configuring-the-solution"></a>Установка и настройка решения
Для установки и настройки решений используйте указанные ниже данные.

* Operations Manager является обязательным компонентом для работы решения "Оценка конфигурации".
* Агент Operations Manager должен быть установлен на каждом компьютере, для которого требуется выполнять оценку конфигурации.
* Решение "Оценка конфигурации" необходимо добавить в рабочую область OMS, как описано в статье [Добавление решений Log Analytics из каталога решений](log-analytics-add-solutions.md).  Дополнительная настройка не требуется.

## <a name="configuration-assessment-data-collection-details"></a>Сведения о сборе данных оценки конфигурации
Оценка конфигурации собирает данные конфигурации, метаданные и данные о состоянии с помощью включенных агентов.

В следующей таблице представлены методы сбора данных и другие сведения о сборе данных для решения "Оценка конфигурации".

| платформа | Direct Agent | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Нет](./media/log-analytics-configuration-assessment/oms-bullet-red.png) |![Да](./media/log-analytics-configuration-assessment/oms-bullet-green.png) |![Нет](./media/log-analytics-configuration-assessment/oms-bullet-red.png) |![Да](./media/log-analytics-configuration-assessment/oms-bullet-green.png) |![Да](./media/log-analytics-configuration-assessment/oms-bullet-green.png) |дважды в день |

В следующей таблице приведены примеры типов данных, собираемых решением "Оценка конфигурации".

| **Тип данных** | **Поля** |
| --- | --- |
| Конфигурация |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Метаданные |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Состояние |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="configuration-assessment-alerts"></a>Оповещения оценки конфигурации
Просматривать оповещения и управлять ими можно на странице "Оповещения" решения "Оценка конфигурации". Оповещения сообщают об обнаруженной проблеме, ее причинах и способах решения. Они также содержат сведения о параметрах конфигурации в вашей среде, которые могут вызывать проблемы производительности.

![просмотр оповещений](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

> [!NOTE]
> Оповещения решения "Оценка конфигурации" отличаются от других оповещений Log Analytics. Для просмотра оповещений требуется подключаемый модуль Silverlight для веб-браузера.
> 
> 

При выборе элемента или категории элементов на странице оповещений будет выведен список серверов или рабочих нагрузок с оповещениями, которые применяются к каждому элементу.

![список оповещений](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Каждое оповещение содержит ссылки на статью базы знаний Майкрософт. В этих статьях содержатся дополнительные сведения об оповещении.

> [!TIP]
> По умолчанию на экран выводится не более 2000 оповещений. Чтобы просмотреть дополнительные оповещения, щелкните панель уведомлений над списком оповещений.
> 
> 

Можно щелкнуть любой элемент в списке, чтобы просмотреть статью базы знаний, которая может помочь устранить причину проблемы, вызвавшей оповещение.

![статья базы знаний](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Вы можете управлять правилами оповещений, игнорируя определенные правила или классы правил.

![управление правилами генерации оповещений](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## <a name="knowledge-recommendations"></a>Рекомендации в базе знаний
При просмотре рекомендаций в базе знаний на экран выводятся результаты поиска в журналах со списком статей базы знаний Майкрософт, рекомендуемых для рабочих нагрузок и компьютеров. Эти статьи содержат дополнительные сведения об оповещениях.

![результаты поиска для рекомендаций в базе знаний](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## <a name="servers-and-workloads-analyzed"></a>Анализируемые серверы и рабочие нагрузки
При просмотре рекомендаций в базы знаний на экран выводятся результаты поиска в журналах со списком серверов и рабочих нагрузок, данные о которых передаются в службу OMS из Operations Manager.

![Серверы и рабочие нагрузки](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## <a name="next-steps"></a>Дальнейшие действия
* Используйте [Поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных по оценке конфигурации.

<!--HONumber=Oct16_HO2-->


