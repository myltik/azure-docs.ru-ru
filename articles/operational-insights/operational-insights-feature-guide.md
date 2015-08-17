<properties
	pageTitle="Руководство по функциям Operational Insights"
	description="Operational Insights — это служба аналитики, которая позволяет ИТ-администраторам получать подробные сведения о локальных и облачных средах. Она позволяет вам работать с обновляемыми в реальном времени и архивными данными о компьютерах, чтобы быстро составлять сводки данных, а также содержит шаблоны анализа данных от корпорации Майкрософт и сообщества."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="banders"/>

# Руководство по функциям Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

В этой статье описываются задачи, которые вам поможет решить служба Operational Insights, составные элементы среды Operational Insights, а также создание учетной записи Operational Insights и вход в службу.

## Преобразование данных компьютера

Operational Insights — это служба аналитики, которая позволяет ИТ-администраторам получать подробные сведения о локальных и облачных средах. Она позволяет вам работать с обновляемыми в реальном времени и архивными данными о компьютерах, чтобы быстро составлять сводки данных, а также содержит шаблоны анализа данных от корпорации Майкрософт и сообщества.

С помощью Operational Insights вы можете преобразовать данные о компьютерах в рабочую информацию, используя следующие элементы.


|**Значок** | **Что использовать** | **Назначение**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [Планирование емкости](operational-insights-capacity.md) | Решение для планирования емкости в Microsoft Azure Operational Insights позволяет оценить емкость серверной инфраструктуры. |
| ![](./media/operational-insights-feature-guide/update.png) | [Оценка системных обновлений](operational-insights-updates.md) | Решение для системных обновлений в Microsoft Azure Operational Insights позволяет применять пропущенные обновления к серверам в инфраструктуре. |
| ![](./media/operational-insights-feature-guide/log-mgt.png) | [Управление журналами](operational-insights-search.md); | Используйте решение «Управление журналами» для сбора событий и журналы служб IIS для поиска журнала в Operational Insights. |
| ![](./media/operational-insights-feature-guide/malware.png) | [Оценка вредоносных программ](operational-insights-antimalware.md) | Для защиты серверов от вредоносных программ можно использовать решение «Защита от вредоносных программ» в Microsoft Azure Operational Insights. |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [Безопасность и порты](operational-insights-security-audit.md) | Используйте «Безопасность и аудит» для исчерпывающего изучения данных об информационной безопасности в вашей организации и определения важных моментов, требующих вашего внимания. |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Оценка SQL и Active Directory.](operational-insights-assessment.md) | Решения оценки можно использовать для оценки риска и работоспособности серверной среды с постоянной периодичностью. |
| ![](./media/operational-insights-feature-guide/alert.png) | [Управление оповещениями](operational-insights-alerts.md) | Для управления оповещениями с серверов, отслеживаемых с помощью Operations Manager, можно использовать решение управления оповещениями. |


Также можно

- **Отправлять данные компьютеров в систему, при этом используя или не используя агент, или совместно с System Center Operations Manager**. Дополнительные сведения см. в следующих статьях:
	- [Подключение к Operational Insights из System Center Operations Manager](operational-insights-connect-scom.md)
	- [Прямое подключение компьютеров к Operational Insights](operational-insights-direct-agent.md)
	- [Анализ данных с серверов в Microsoft Azure](operational-insights-analyze-data-azure.md)
- **Выполните все вышеперечисленное во время работы с мобильным приложением**
	- Дополнительные сведения о приложении Windows Phone см. в разделе [Мобильные приложения Operational Insights](http://www.windowsphone.com/ru-ru/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)

## Среда Operational Insights

Среда Operational Insights состоит из:

- размещенных в Microsoft Azure рабочих областей, которые являются контейнерами для учетных записей Azure;
- веб-службы Operational Insights, размещаемой в облаке;
- отдельных агентов, которые подключаются непосредственно к веб-службе;
- или службы, подключенной к System Center Operations Manager (необязательно).


Если вы использовали предыдущую версию Operational Insights под названием System Center Advisor, эта программа может быть установлена в вашей локальной среде. Тем не менее, программа Advisor не поддерживается службой Operational Insights.

Использование программы Operational Insights в качестве службы Operations Manager включает одну или несколько групп управления и не менее одного агента на каждую из них. Агенты Operations Manager собирают данные с ваших серверов и анализируют их с помощью решений (аналогично пакетам управления в System Center Operations Manager). Данные регулярно отправляются из Operations Manager в веб-службу Operational Insights (при необходимости, через прокси-сервер). При этом ничего не сохраняется в базах данных Operations Manager, поэтому они не имеют дополнительной нагрузки.

Аналогичным образом, агенты, установленные на отдельных компьютерах, могут подключаться непосредственно к веб-службе, чтобы отправлять собранные данные на обработку.

Данные каждого решения анализируются и представляются на портале Operational Insights. Затем вы можете просматривать оповещения и соответствующие рекомендации по устранению неполадок, оценки конфигурации, проблемы с мощностью инфраструктуры, состояние обновления системы, предупреждения о вредоносном ПО и данные журналов. Вы также можете осуществить специальный поиск по журналам и исследования.

![Изображение общей схемы Operational Insights](./media/operational-insights-feature-guide/environment.png)

### Где находится Operational Insights?
Microsoft Azure Operational Insights находится в США. Несмотря на то, что рабочим языком Operational Insights является английский язык, служба доступна на некоторых других рынках. Дополнительные сведения см. в разделе [Международный доступ](http://go.microsoft.com/fwlink/?LinkId=229842).

<!---HONumber=August15_HO6-->