<properties 
   pageTitle="Поддерживаемые конфигурации для службы оперативной аналитики"
   description="Информация о конфигурациях для работы службы оперативной аналитики"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" /> 
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Поддерживаемые конфигурации для службы оперативной аналитики

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Что нужно для использования службы оперативной аналитики? Сверьтесь со следующей информации, чтобы подготовиться к использованию службы оперативной аналитики.


## Конфигурации для System Center 2012 Operations Manager

Вы можете использовать службу оперативной аналитики как подключенную службу в Operations Manager в System Center 2012 R2 или System Center 2012 SP1 R2. Это позволяет использовать консоль управления в Operations Manager для просмотра оповещений службы оперативной аналитики и информации о конфигурации. Если вы используете службу оперативной аналитики в качестве подключенной службы в Operations Manager, агент взаимодействует напрямую с сервером управления, который, в свою очередь, взаимодействует со службой оперативной аналитики.

Чтобы использовать службу оперативной аналитики в качестве подключенной службы, необходимо выполнить следующие необходимые условия.


- Для интеграции System Center 2012 SP1 Operations Manager и службы оперативной аналитики требуются обновленные пакеты управления, включенные в [соединитель оперативной аналитики для Operations Manager](https://www.microsoft.com/ru-ru/download/details.aspx?id=38199). Вы можете загрузить и установить пакеты управления со страницы [Соединитель оперативной аналитики для Operations Manager](https://www.microsoft.com/ru-ru/download/details.aspx?id=38199) в Центре загрузки.

- System Center 2012 SP1: накопительный пакет обновления 6 для Operations Manager, хотя более предпочтителен накопительный пакет обновления 7. Это обновление необходимо применить в качестве сценария подключенной службы к серверу управления, агентам и консоли управления службой оперативной аналитики.

- System Center 2012 R2: накопительный пакет обновления 2 для Operations Manager, хотя более предпочтителен накопительный пакет обновления 3. Это обновление необходимо применить в качестве сценария подключенной службы к серверу управления, агентам и консоли управления службой оперативной аналитики.

- Чтобы просмотреть данные управления емкостью, необходимо разрешить подключение Operations Manager к Virtual Machine Manager (VMM). Дополнительную информацию о подключении систем см в статье [Подключение VMM к Operations Manager](https://technet.microsoft.com/ru-ru/library/hh882396.aspx).

Инструкции по установке и настройке см. в статье [Просмотр оповещений службы оперативной аналитики](http://go.microsoft.com/fwlink/?LinkID=293793).

Если вы хотите просматривать оповещения службы оперативной аналитики о SharePoint Server 2010, Lync Server 2013, Lync Server 2010 или System Center 2012 SP1 — Virtual Machine Manager, необходимо настроить учетную запись запуска от имени для этих рабочих нагрузок. См. следующую информацию:


- [Настройка учетной записи запуска от имени для SharePoint](../operational-insights-run-as.md)

- [Настройка учетной записи запуска от имени для Lync Server](../operational-insights-run-as.md)

- [Настройка учетной записи запуска от имени для Virtual Machine Manager](../operational-insights-run-as.md)

### Операционные системы Operations Manager

Агенты Operations Manager поддерживаются на разных компьютерах. Дополнительную информацию о поддержке агента см. в статье [Подготовка среды для System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx).

### Необходимое программное обеспечение для Operations Manager

Чтобы просмотреть данные управления емкостью, необходимо разрешить подключение Operations Manager к Virtual Machine Manager. Дополнительную информацию о подключении систем см в статье [Подключение VMM к Operations Manager](https://technet.microsoft.com/ru-ru/library/hh882396.aspx).

## Агенты подключенные непосредственно к службе оперативной аналитики

Для подключения к службе напрямую используется агент Microsoft Monitoring Agent Его требования к системе перечислены на странице [Центра загрузки Майкрософт](https://www.microsoft.com/ru-ru/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True).

## Браузеры

Со службой оперативной аналитики можно использовать любой из следующих браузеров:

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 или Internet Explorer 7

- Mozilla Firefox 3.5 или более поздней версии

Независимо от используемого браузера также необходимо установить Microsoft Silverlight 4.

## Технологии, которые можно анализировать

Служба оперативной аналитики анализирует следующие рабочие нагрузки:

- Windows Server 2012 и Microsoft Hyper-V Server 2012

- Windows Server 2008 R2, Windows Server 2008 и Windows Server 2012 R2.
    - Active Directory
	- Узел Hyper-V
	- Общая операционная система

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - SQL Server Database Engine

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 и Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 и Lync Server 2010

- System Center 2012 SP1 — Virtual Machine Manager

Для SQL Server поддерживается анализ следующих 32-разрядных и 64-разрядных выпусков:

- SQL Server 2008 и 2008 R2 Enterprise

- SQL Server 2008 и 2008 R2 Standard

- SQL Server 2008 и 2008 R2 Workgroup

- SQL Server 2008 и 2008 R2 Web

- SQL Server 2008 и 2008 R2 Express

Кроме того, 32-разрядный выпуск SQL Server поддерживается при работе в режиме реализации WOW64.


<!--HONumber=54--> 