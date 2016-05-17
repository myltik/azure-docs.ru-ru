<properties
	pageTitle="Добавление решений Log Analytics из коллекции решений | Microsoft Azure"
	description="Решения Log Analytics представляют собой коллекцию логики, визуализации и правил получения данных, предоставляющую метрики, связанные с определенной проблемной областью."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Добавление решений Log Analytics из коллекции решений

Решения Log Analytics представляют собой коллекцию **логики**, **визуализации** и **правил получения данных**, предоставляющую метрики, связанные с определенной проблемной областью. В этой статье указаны решения, поддерживаемые службой Log Analytics, и рассказывается, как добавлять их и удалять из коллекции решений.

Решения позволяют получить более подробные данные для:
- изучения и более быстрого решения проблем с работоспособностью;
- сбора и сопоставления различных типов машинных данных;
- заблаговременно принимать такие меры, как планирование емкости, создание отчетов о состоянии исправлений и аудит системы безопасности.


>[AZURE.NOTE] OMS включает базовые функции поиска по журналам, поэтому для активации решения устанавливать его необязательно. Однако для получения дополнительных функциональных возможностей в службу можно добавить решения со страницы "Коллекция решений".

После добавления решения данные собираются с серверов в вашей инфраструктуре и отправляются в службу OMS. Обработка с помощью службы OMS может занять от нескольких минут до нескольких часов. Обработанные данные можно просмотреть в службе OMS.

Если решение больше не нужно, его можно удалить. При удалении решения его данные не отправляются в службу OMS, что приводит к сокращению объема данных, используемых в рамках дневной квоты (если есть).


## Решения, поддерживаемые агентом Microsoft Monitoring Agent

В настоящее время серверы, подключенные к OMS с помощью Microsoft Monitoring Agent, могут использовать большую часть доступных решений, включая:

- Обновление системы;
- Защита от вредоносных программ;
- Отслеживание изменений
- Оценка SQL
- Оценка Active Directory
- Управление оповещениями (без оповещений SCOM)

Тем не менее следующие решения *не* поддерживаются Microsoft Monitoring Agent, и для них необходим агент System Center Operations Manager (SCOM).

- Управление емкостью;
- Управление оповещениями (с оповещениями SCOM)
- Оценка конфигурации

Сведения о подключении агента SCOM к службе Log Analytics см. в статье [Connecting Operations Manager to Log Analytics](log-analytics-om-agents.md) (Подключение Operations Manager к службе Log Analytics).

### Добавление решения с использованием коллекции решений

1. На странице "Обзор" в службе OMS щелкните плитку **Коллекция решений**. ![коллекция решений](./media/log-analytics-add-solutions/sol-gallery.png)
2. На странице "Коллекция решений OMS" можно узнать о каждом доступном решении. Щелкните имя решения, которое нужно добавить в OMS.
3. На странице выбранного решения отображаются подробные сведения о нем. Щелкните **Добавить**.
4. На странице "Обзор" в службе OMS появится новая плитка для решения, которую можно начать использовать после того, как служба OMS обработает данные.

## Настройка решений
1. Некоторые решения необходимо настраивать. Например, перед использованием необходимо настроить службы автоматизации, архивации и Azure Site Recovery.
2. Для любого из этих решений щелкните соответствующую плитку на странице "Обзор". ![настройка решения](./media/log-analytics-add-solutions/configure-additional.png)
3. Затем настройте решение, указав необходимые данные, и нажмите кнопку **Сохранить**. ![настройка решения](./media/log-analytics-add-solutions/configure.png)

### Удаление решения с использованием коллекции решений

1. На странице "Обзор" в службе OMS щелкните плитку **Параметры**.
2. На вкладке "Решения" страницы "Параметры" нажмите кнопку **Удалить** рядом с соответствующим решением.
3. В диалоговом окне подтверждения нажмите кнопку **Да**, чтобы удалить решение.

## Сведения о сборе данных для компонентов и решений OMS

В следующей таблице представлены методы сбора данных и другие сведения о сборе данных по компонентам и решениям OMS.

|тип данных| платформа | Direct Agent | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
|---|---|---|---|---|---|---|---|
|Оценка AD|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 дней|
|Состояние репликации AD|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 дней|
|Оповещения (Nagios)|Linux|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|при получении|
|Оповещения (Zabbix)|Linux|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 минута|
|Оповещения (Operations Manager)|Windows|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 минуты|
|Защита от вредоносных программ;|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)| ежечасно|
|Управление емкостью;|Windows|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)| ежечасно|
|Отслеживание изменений|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)| ежечасно|
|Отслеживание изменений|Linux|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|ежечасно|
|Оценка конфигурации (устаревший консультант)|Windows|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)| дважды в день|
|Трассировка событий Windows|Windows|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 мин|
|Журналы IIS|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 мин|
|группы сетевой безопасности;|Windows|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 минут|
|Office 365|Windows|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|при уведомлении|
|Счетчики производительности|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|по расписанию, не менее 10 секунд|
|Счетчики производительности|Linux|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|по расписанию, не менее 10 секунд|
|Service Fabric|Windows|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 мин|
|Оценка SQL|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 дней|
|SurfaceHub|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|при получении|
|Syslog|Linux|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|из хранилища Azure — 10 минут, из агента — при получении|
|Обновление системы;|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)| не меньше 2 раз в день и через 15 минут после установки обновления|
|Журналы событий безопасности Windows|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)| для хранилища Azure — 10 мин, для агента — при получении|
|Журналы брандмауэра Windows|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)| при получении|
|Журналы событий Windows|Windows|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)| для хранилища Azure — 1 мин, для агента — при получении|
|Проводные данные|Windows (2012 R2/8.1 или более поздней версии)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Да](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Нет](./media/log-analytics-add-solutions/oms-bullet-red.png)| каждую минуту|


## Дальнейшие действия

- Используйте [поиск по журналам](log-analytics-log-searches.md) для просмотра подробных сведений, собранных решениями.

<!---HONumber=AcomDC_0504_2016-->