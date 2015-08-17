<properties
   pageTitle="Просмотр оповещений из Operations Manager"
   description="Узнайте об управлении оповещениями из Operations Manager для отслеживаемых серверов в инфраструктуре."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders" />



# Просмотр оповещений Operations Manager

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Перед использованием функции управления оповещениями в Microsoft Azure Operational Insights необходимо установить решение. Дополнительные сведения об установке решений см. в разделе [Установки рабочей области](operational-insights-setup-workspace.md). Решение работает только в том случае, если ваши серверы отслеживаются агентами Operations Manager. Дополнительные сведения об использовании Operations Manager с Operational Insights см. в разделе [Подключение к Operational Insights из System Center Operations Manager](operational-insights-connect-scom.md).

После установки решения можно просмотреть оповещения для отслеживаемых серверов, воспользовавшись плиткой **Управление оповещениями** на панели мониторинга **Обзор** в службе Operational Insights.

![image of Alert Management tile](./media/operational-insights-alerts/overview-alert.png)


Просмотр оповещений Microsoft Azure Operational Insights и управление ими осуществляется на панели мониторинга **Оповещения**. На панели мониторинга сведения об оповещениях отображаются в следующих категориях:

- активные оповещения;
	- критические оповещения;
	- предупреждающие оповещения;
	- источники оповещений;
- все активные оповещения.
	- Показывает процент оповещений, которые являются критическими, предупреждающими и информационными.
- Общие запросы оповещений
	- В этой области находятся готовые запросы, созданные командой разработчиков программного обеспечения Operational Insights, чтобы помочь вам приступить к работе с оповещениями.


Оповещения информируют об обнаружении проблемы, затронутом сервере, а также выводят данные о приоритете и имени предупреждения.

![image of Alert dashboard](./media/operational-insights-alerts/alert-drilldown1.png)

![image of Alert dashboard](./media/operational-insights-alerts/alert-drilldown2.png)


На панели мониторинга **Управление оповещениями** можно просмотреть все оповещения, обнаруженные службой Microsoft Azure Operational Insights.

## Просмотр оповещений Operational Insights

1. На странице **Обзор** щелкните плитку **Управление оповещениями**.
2. На панели мониторинга **Управление оповещениями** просмотрите категории оповещений и выберите одну для работы.
3. Щелкните плитку или любой элемент, чтобы просмотреть подробную информацию о нем на странице **Поиск**.
4. С помощью полученной информации можно изучить оповещение и определить действия, которые могут потребоваться для его устранения.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->