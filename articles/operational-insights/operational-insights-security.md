<properties
	pageTitle="Безопасность Operational Insights"
	description="Узнайте, как Operational Insights сохраняет конфиденциальность и защищает данные."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="banders"/>

# Безопасность Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Корпорация Майкрософт заботится о вашей конфиденциальности и безопасности ваших данных, выпуская программное обеспечение и службы, которые помогают вам управлять ИТ-инфраструктурой своей организации. Мы понимаем, что когда вы доверяете свои данные сторонним организациям, они требуют надежной защиты. Корпорация Майкрософт следует строгим нормативным требованиям и указаниям по безопасности — от создания кода до эксплуатации служб.

Защита данных — главный приоритет корпорации Майкрософт. Отправляйте свои вопросы, предложения и сообщения о неполадках, связанные с представленными ниже сведениями, включая наши политики защиты, в [службу поддержки Azure](http://azure.microsoft.com/support/options/).

В этой статье описываются сбор, обработка и защита данных в службе Microsoft Azure Operational Insights. Вы можете либо использовать агенты для непосредственного подключения к веб-службе, либо использовать службу System Center Operations Manager, чтобы собирать рабочие данные для службы Operational Insights. Собранные данные отправляются по Интернету в службу Operational Insights, которая размещается в Microsoft Azure.

Служба Operational Insights безопасно управляет данными с использованием следующих методов:

**Разделение данных.** Данные клиентов логическим образом отделяются для каждого компонента службы Operational Insights. Все данные отмечаются тегами по организациям. Эти теги существуют в течение всего жизненного цикла данных и используются на каждом уровне службы.

У каждого клиента есть выделенный большой двоичный объект Azure для долгосрочного хранения данных. Этот объект шифруется уникальными ключами клиентов, которые меняются каждые 90 дней.

**Хранение данных.** Объединенные метрики для некоторых решений (прежнее название — пакеты аналитики), например для управления емкостью, хранятся в базе данных SQL, размещенной в Microsoft Azure. Эти данные хранятся 390 дней. Индексированные данные поиска по журналу хранятся согласно ценовому плану. Дополнительные сведения см. на [странице цен](http://azure.microsoft.com/pricing/details/operational-insights/)

**Физическая безопасность.** Службой Operational Insights управляет персонал корпорации Майкрософт, а все действия записываются в журнал и доступны для аудита. Служба Operational Insights полностью выполняется в Azure и соответствует общим условиям проектирования Azure. Вы можете просмотреть сведения о физической защите активов Azure на странице 18 [обзора безопасности Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf).

**Нормативные требования и сертификация**. Группа разработки и обслуживания Operational Insights активно сотрудничает с юридическим отделом и группой обеспечения соответствия требованиям корпорации Майкрософт, включая стандарт ISO.

В настоящий момент соблюдаются следующие стандарты безопасности:

- Общие условия проектирования для Windows
- Сертификация защиты информационных систем Майкрософт


## Безопасность потока данных
На следующей схеме показан поток информации из вашей компании и то, как он защищается по мере перемещения в службу Operational Insights, пока, наконец, вы не видите эти данные в Operational Insights. Под схемой приведена дополнительная информация о каждом шаге.

![Иллюстрация сбора и защиты данных в Operational Insights](./media/operational-insights-security/security.png)

### 1\. Регистрация в Operational Insights и сбор данных

Чтобы организация могла отправлять данные в службу Operational Insights, необходимо настроить агенты Microsoft Monitoring Agent, если используется непосредственное подключение к веб-службе, или использовать мастер настройки в консоли управления в Operations Manager. Пользователям (это можете быть вы, другие индивидуальные пользователи или группа людей) необходимо создать одну или несколько учетных записей Operational Insights и зарегистрировать каждый агент, подключаемый напрямую, или свою среду Operations Manager с помощью одной из следующих учетных записей:


- [идентификатор организации;](../sign-up-organization.md)

- [Учетная запись Майкрософт — Outlook, Office Live, MSN.](../sign-up-organization.md)

Учетная запись Operational Insights — это место, где данные собираются, вычисляются, анализируются и представляются. Учетная запись Operational Insights в основном используется для секционирования данных, и каждая учетная запись Operational Insights уникальна. Например, может потребоваться управлять рабочими данными с помощью одной учетной записи Operational Insights, а тестовыми данными — с помощью другой. Учетные записи также помогают администратору управлять доступом пользователей к данным. С каждой учетной записью Operational Insights может быть связано несколько учетных записей пользователей, и у каждой учетной записи пользователя может быть несколько учетных записей Operational Insights.

После завершения мастера настройки каждая группа управления Operations Manager устанавливает подключение к службе Operational Insights. Затем с помощью мастера добавления компьютеров можно будет выбрать, каким компьютерам в группе управления разрешается отправлять данные в службу.

Оба типа агентов собирают данные для Operational Insights. Тип собираемых данных зависит от типов используемых решений. Решением является набор заранее определенных представлений, запросов поиска в журналах, правил сбора данных и логики обработки. Только администраторы Operational Insights могут использовать Operational Insights для импорта решения. После импорта решение перемещается на серверы управления Operations Manager (если используется), а затем в агенты Operations Manager, которые вы выбрали. После этого агенты собирают данные.

В следующей таблице перечислены доступные решения в Operational Insights и типы данных, которые они собирают.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Решение</b></td>
		<td><b>Типы данных</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Оценка конфигурации</td>
		<td>Данные конфигурации, метаданные и данные о состоянии.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Планирование емкости</td>
		<td>Данные производительности, метаданные и данные о состоянии.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Защита от вредоносных программ;</td>
		<td>Данные конфигурации, метаданные и данные о состоянии.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Оценка системных обновлений</td>
		<td>Метаданные и данные о состоянии.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Управление журналами;</td>
		<td>Пользовательские журналы событий.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Отслеживание изменений</td>
		<td>Данные инвентаризации программного обеспечения и метаданные службы Windows.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Оценка SQL и Active Directory.</td>
		<td>Данные WMI, данные реестра, данные производительности и результаты динамического управления SQL Server.</td>
    </tr>
    </tbody>
    </table>


В следующей таблице показаны примеры типов данных:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Тип данных</b></td>
		<td><b>Поля</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Предупреждение</td>
		<td>Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount</td>
    </tr>
    <tr align="left" valign="top">
		<td>Конфигурация</td>
		<td>CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate</td>
    </tr>
	<tr align="left" valign="top">
		<td>Событие</td>
		<td>EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded<p><b>Примечание.</b> Operational Insights собирает данные событий с настраиваемыми полями при их регистрации в журнале событий Windows. </td>
    </tr>
	    <tr align="left" valign="top">
		<td>Metadata</td>
		<td>BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime</td>
    </tr>
    <tr align="left" valign="top">
		<td>Производительность</td>
		<td>ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded</td>
    </tr>
    <tr align="left" valign="top">
		<td>Состояние</td>
		<td>StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified</td>
    </tr>
    </tbody>
    </table>


### 2\. Отправка данных от агентов

Если используются агенты, которые подключаются непосредственно к веб-службе, их необходимо зарегистрировать с помощью ключа и установить безопасное подключение между агентом и службой Operational Insights с использованием порта 443.

Если используется Operations Manager, в регистрируете учетную запись в службе Operational Insights и устанавливаете безопасное подключение HTTPS между сервером управления Operations Manager и службой Operational Insights через порт 443. Если Operations Manager по какой-либо причине не удается подключиться к службе, собранные данные сохраняются во временном кэше, и сервер управления пытается повторно отправить данные каждые 8 минут в течение 2 часов. Собранные данные сжимаются и отправляются в службу Operational Insights, минуя локальные базы данных, то есть они не нагружаются дополнительно. После отправки собранных данных они удаляются из кэша.

### 3\. Служба Operational Insights получает и обрабатывает данные

Служба Operational Insights обеспечивает надежность источника входящих данных, проверяя сертификаты и целостность данных. Затем необработанные данные сохраняются как большой двоичный объект в [службе хранилища Microsoft Azure](http://azure.microsoft.com/documentation/services/storage/). Каждому пользователю Operational Insights выделяется большой двоичный объект Azure, доступный только ему. Тип хранящихся данных зависит от типов решений, которые были импортированы и используется для сбора данных.

Служба Operational Insights обрабатывает необработанные данные, и вычисленные обработанные данные сохраняются в базе данных SQL. Для обмена данными между службой Operational Insights и базой данных SQL используется аутентификация базы данных SQL.

### 4\. Использование Operational Insights для доступа к данным

Вы можете войти в Operational Insights с помощью учетной записи, настроенной ранее. Весь трафик между Operational Insights и службой Operational Insights передается через защищенный канал HTTPS.

<!---HONumber=July15_HO3-->