<properties 
    pageTitle="Пакеты аналитики Operational Insights" 
    description="Пакеты аналитики позволяют расширять функциональность службы Operational Insights." 
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
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Пакеты аналитики Operational Insights

В состав Microsoft Azure Operational Insights входит базовый модуль оценки конфигурации. Однако для получения дополнительных функциональных возможностей в службу можно добавить пакты аналитики со страницы "Обзор".

![image of intelligence packs icon](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

После добавления пакета аналитики данные собираются с серверов в инфраструктуре и отправляются в службу Operational Insights. Обработка с помощью службы Operational Insights может занять от нескольких минут до нескольких часов. Обработанные данные можно просмотреть в службе Operational Insights.

Если пакет аналитики больше не нужен, его можно удалить. При удалении пакета аналитики его данные не отправляются в службу Operational Insights, что приводит к сокращению объема данных, используемых в рамках дневной квоты.

## Пакеты аналитики поддерживаются агентом Microsoft Monitoring Agent

В настоящее время серверы, которые напрямую подключены к службе Microsoft Azure Operational Insights с помощью агента Microsoft Monitoring Agent, могут использовать большую часть доступных пакетов аналитики, включая:

- [Обновление системы](operational-insights-updates.md)

- [Управление журналами](operational-insights-log-collection.md)

- [Защита от вредоносных программ](operational-insights-antimalware.md)

- [Отслеживание изменений](operational-insights-change-tracking.md)

- [Оценка SQL и Active Directory](operational-insights-assessment.md)

Однако агент Microsoft Monitoring Agent  *не* поддерживает следующие пакеты аналитики:

- [Управление емкостью](operational-insights-capacity.md)

- [Оценка конфигурации](operational-insights-configuration-assessment.md)

Сбор данных журналов IIS поддерживается на компьютерах под управлением следующих ОС:

- Windows Server 2012

- Windows Server 2012 R2

### Добавление пакета аналитики


1. На странице "Обзор" в службе Operational Insights щелкните плитку **Пакеты аналитики**.


2. На странице "Коллекция пакетов аналитики Operational Insights" можно получить сведения о каждом доступном пакете аналитики. Щелкните имя пакета аналитики, который нужно добавить в службу Operational Insights.


3. На странице выбранного пакета аналитики отображаются подробные сведения об этом пакете. Щелкните **Добавить**.


4. На странице подтверждения нажмите кнопку **Принимаю**, чтобы принять условия заявления о конфиденциальности и условия использования.


5. На странице "Обзор" в службе Operational Insights появится новая плитка для пакета аналитики, которую можно начать использовать после того, как служба Operational Insights обработает данные.




### Удаление пакета аналитики



1. На странице "Обзор" в службе Operational Insights щелкните плитку **Пакеты аналитики**.


2. На странице "Коллекция пакетов аналитики" выберите пакет аналитики и нажмите кнопку **Удалить**.


3. На странице подтверждения нажмите кнопку **Да**, чтобы удалить пакет аналитики.



<!--HONumber=52-->