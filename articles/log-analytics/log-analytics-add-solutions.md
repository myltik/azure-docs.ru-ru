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


>[AZURE.NOTE] Log Analytics включает функции поиска по журналам, поэтому для активации решения устанавливать его необязательно. Однако для получения визуализации данных, рекомендуемых условий поиска и аналитических данных можно добавить решения из коллекции решений.

После добавления решения данные собираются с серверов в вашей инфраструктуре и отправляются в службу OMS. Обычно обработка в OMS занимает от нескольких минут до часа. Обработанные данные можно просмотреть в службе OMS.

Если решение больше не нужно, его можно удалить. При удалении решения его данные не отправляются в службу OMS, что приводит к сокращению объема данных, используемых в рамках дневной квоты (если есть).


## Решения, поддерживаемые агентом Microsoft Monitoring Agent

В настоящее время серверы, подключенные к OMS с помощью Microsoft Monitoring Agent, могут использовать большую часть доступных решений, включая:

- Оценка Active Directory
- Управление оповещениями (без оповещений SCOM)
- Защита от вредоносных программ;
- Отслеживание изменений
- Безопасность
- Оценка SQL
- Обновление системы;

Тем не менее следующие решения *не* поддерживаются Microsoft Monitoring Agent, и для них необходим агент System Center Operations Manager (SCOM).

- Управление оповещениями (с оповещениями SCOM)
- Управление емкостью;
- Оценка конфигурации

Сведения о подключении агента SCOM к службе Log Analytics см. в статье [Connecting Operations Manager to Log Analytics](log-analytics-om-agents.md) (Подключение Operations Manager к службе Log Analytics).

### Добавление решения с использованием коллекции решений

1. На странице "Обзор" в службе OMS щелкните элемент **Коллекция решений**. ![коллекция решений](./media/log-analytics-add-solutions/sol-gallery.png)
2. На странице "Коллекция решений OMS" можно узнать о каждом доступном решении. Щелкните имя решения, которое нужно добавить в OMS.
3. На странице выбранного решения отображаются подробные сведения о нем. Щелкните **Добавить**.
4. На странице "Обзор" в службе OMS появится новая плитка для решения, которую можно начать использовать после того, как служба OMS обработает данные.

## Настройка решений
1. Некоторые решения необходимо настраивать. Например, перед использованием необходимо настроить службы автоматизации, архивации и Azure Site Recovery.
2. Для любого из этих решений щелкните соответствующий элемент на странице "Обзор". ![настройка решения](./media/log-analytics-add-solutions/configure-additional.png)
3. Затем настройте решение, указав необходимые данные, и нажмите кнопку **Сохранить**. ![настройка решения](./media/log-analytics-add-solutions/configure.png)

### Удаление решения с использованием коллекции решений

1. На странице "Обзор" в службе OMS щелкните элемент **Параметры**.
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

## Решения и функции предварительной версии службы Log Analytics

Запуск службы и выполнение процедур разработки позволяет сотрудничать с клиентами в процессе разработки функций и решений.

На этапе работы с закрытой предварительной версии мы предоставляем небольшой группе клиентов доступ к ранней реализации функции или решения, чтобы получить обратную связь и внести усовершенствования. Ранняя реализация включает минимум функций и операционных возможностей.

Наша цель — быстро опробовать новые функции и понять, что работает, а что нет. Мы работаем таким образом до тех пор, пока отзывы пользователей закрытой предварительной версии не покажут, что мы готовы к выпуску общедоступной предварительной версии.

В общедоступной предварительной версии функция или решение становятся доступными для всех пользователей, что позволяет получить больше отзывов и проверить нашу оценку и эффективность. На этом этапе

- Предварительные версии функций появляются на вкладке "Параметры" и могут быть активированы пользователем.
- Предварительные версии решений можно добавлять из коллекции или с помощью опубликованного скрипта.

### Что нужно знать о предварительных версиях компонентов и решений?

Мы всегда с радостью выпускаем новые функции и решения и обожаем сотрудничать с вами в процессе их разработки.

Однако предварительные версии функций и решений подходят не всем, поэтому прежде, чем подавать запрос об участии в тестировании закрытой предварительной версии или активировать общедоступную предварительную версию, убедитесь, что вы сможете работать с сырым продуктом.

Активировав предварительную версию функции через портал, вы увидите предупреждение о том, что эта функция находится на этапе предварительной версии.

#### Для *закрытых* и *общедоступных* предварительных версий

И к закрытой, и к общедоступной предварительным версиям относится следующее.

- Они могут работать некорректно. 
  - Проблемы могут быть разными, от незначительных раздражающих факторов до абсолютно неработающих функций.
- Предварительная версия может негативно отразиться на ваших системах или среде.
  - Мы стараемся избежать негативного влияния на системы, используемые с OMS, но иногда происходит непредвиденное.
- Возможна потеря или повреждение данных.
- Мы можем запрашивать у вас журналы диагностики или другие данные для устранения проблем.
- Функция или решение могут быть удалены (временно или навсегда).
  - В зависимости от результатов работы на этапе предварительной версии мы можем отказаться от выпуска функции или решения.
- Предварительные версии могут не работать или тестироваться со всеми конфигурациями; кроме того, мы можем ограничить следующее.
  - Операционные системы, которые можно использовать (например, на этапе предварительной версии функция может применяться только к Linux).
  - Тип агента (MMA, SCOM), который можно использовать (например, на этапе предварительной версии функция может не работать с SCOM).  
- Предварительные версии решений и функций не охватываются соглашением об уровне обслуживания.
- Использование функций в предварительной версии требует платы за использование.
- Функции и возможности, необходимые для эффективной работы функции или решения, могут отсутствовать или быть неполными.
- Компоненты и решения могут быть доступны не во всех регионах.
- Компоненты и решения могут быть не локализованы.
- Компоненты и решения могут иметь ограничение по числу клиентов или устройств, которые могут его использовать.
- Для настройки конфигурации и включения решения или функции могут потребоваться скрипты.
- Пользовательский интерфейс может быть неполным и периодически меняться.
- Общедоступные предварительные версии могут не подойти для ваших производственных или критически важных систем.

#### Для *закрытых* предварительных версий

Помимо вышеизложенного, к закрытым предварительным версиям относится следующее.

- Мы рассчитываем на обратную связь с вашей стороны — это поможет нам усовершенствовать функцию или решение.
- Мы можем обращаться к вам за обратной связью с помощью опросов, по телефону и по электронной почте.
- Предварительные версии могут работать некорректно.
- Для участия может потребоваться соглашение о неразглашении, так как предварительные версии могут включать конфиденциальное содержимое.
  - Прежде чем писать в блог или Twitter либо сообщать что-либо третьим сторонам, уточните у руководителя программы, ответственного за предварительную версию, какие ограничения связаны с разглашением информации.
- Предварительные версии не стоит использовать в производственных и критически важных системах.


### Как получить доступ к закрытым предварительным версиям функций и решений?

Мы приглашаем клиентов участвовать в тестировании предварительных версий различными способами в зависимости от конкретной функции или решения.

- Участвуя в ежемесячных опросах клиентов и предоставив нам разрешение на дальнейшую связь, вы повышаете свои шансы на участие в тестировании.
- Ваше участие в тестировании может быть предложено менеджером по работе с клиентами Майкрософт.
- Данные о регистрации опубликованы в Twitter [msopsmgmt](https://twitter.com/msopsmgmt). 
- Данные о регистрации распространяются на общественных мероприятиях — ищите нас на собраниях, конференциях и в онлайн-сообществах. 


## Дальнейшие действия

- Используйте [поиск по журналам](log-analytics-log-searches.md) для просмотра подробных сведений, собранных решениями.

<!---HONumber=AcomDC_0518_2016-->