<properties
    pageTitle="Решения Operational Insights"
    description="Вы можете добавлять решения, чтобы расширять функциональные возможности Operational Insights."
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Решения Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

В состав Microsoft Azure Operational Insights входит базовый модуль оценки конфигурации. Однако для получения дополнительных функциональных возможностей в службу можно добавить решения со страницы «Обзор».

![Изображение значка решения](./media/operational-insights-add-solution/sol-gallery.png)

После добавления решения данные собираются с серверов в вашей инфраструктуре и отправляются в службу Operational Insights. Обработка с помощью службы Operational Insights может занять от нескольких минут до нескольких часов. Обработанные данные можно просмотреть в службе Operational Insights.

Если решение больше не нужно, его можно удалить. При удалении решения его данные не отправляются в службу Operational Insights, что приводит к сокращению объема данных, используемых в рамках дневной квоты.

## Решения, поддерживаемые агентом Microsoft Monitoring Agent

В настоящее время серверы, которые напрямую подключены к службе Microsoft Azure Operational Insights с помощью Microsoft Monitoring Agent, могут использовать большую часть доступных решений, включая:

- [Обновление системы](operational-insights-updates.md);

- [Управление журналами](operational-insights-log-collection.md);

- [Защита от вредоносных программ](operational-insights-antimalware.md);

- [Отслеживание изменений](operational-insights-change-tracking.md);

- [Оценка SQL и Active Directory](operational-insights-assessment.md).

Тем не менее следующие решения *не* поддерживаются Microsoft Monitoring Agent, и для них необходим System Center Operations Manager (SCOM).

- [Управление емкостью](operational-insights-capacity.md);

- [Управление оповещениями](operational-insights-alerts.md)

- [Оценка конфигурации](operational-insights-solutions.md#configuration-assessment).

Рекомендации по использованию этих решений с Operations Manager см. в разделе [Факторы, которые необходимо учитывать при использовании Operations Manager с Operational Insights](operational-insights-operations-manager.md).

Сбор данных журналов IIS поддерживается на компьютерах под управлением следующих ОС:

- Windows Server 2012

- Windows Server 2012 R2

### Добавление решения


1. На странице «Обзор» в службе Operational Insights щелкните плитку **Коллекция решений**.


2. На странице «Коллекция решений Operational Insights» можно узнать о каждом доступном решении. Щелкните имя решения, которое нужно добавить в Operational Insights.


3. На странице выбранного решения отображаются подробные сведения о нем. Щелкните **Добавить**.


4. На странице подтверждения нажмите кнопку **Принимаю**, чтобы принять условия заявления о конфиденциальности и условия использования.


5. На странице «Обзор» в службе Operational Insights появится новая плитка для решения, которую можно начать использовать после того, как служба Operational Insights обработает данные.




### Удаление решения



1. На странице «Обзор» в службе Operational Insights щелкните плитку **Коллекция решений**.


2. На странице «Коллекция решений» нажмите кнопку **Удалить** под удаляемым решением.


3. На странице подтверждения нажмите кнопку **Да**, чтобы удалить решение.

<!---HONumber=July15_HO2-->