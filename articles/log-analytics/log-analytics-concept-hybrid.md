---
title: Сбор данных из среды с помощью Azure Log Analytics | Документация Майкрософт
description: В этой статье содержатся сведения о том, как собирать данные и наблюдать за компьютерами, размещенными в локальной или другой облачной среде, с помощью Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: magoedte
ms.openlocfilehash: 9346e9a9ad310a21c6d6ce388b76ce491041289c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Сбор данных с компьютеров в среде с помощью Log Analytics

Azure Log Analytics позволяет собирать и обрабатывать данные компьютеров с ОС Windows или Linux, размещенные:

* на [виртуальных машинах Azure](log-analytics-quick-collect-azurevm.md) с помощью расширения виртуальной машины Log Analytics; 
* в центре обработки данных в качестве физических серверов или виртуальных машин;
* на виртуальных машинах в облачной службе (например, Amazon Web Services (AWS)).

Компьютеры, размещенные в среде, можно подключать непосредственно к службе Log Analytics. Если вы уже проводите мониторинг этих компьютеров с помощью System Center Operations Manager 2012 R2 или 2016, можно интегрировать группы управления Operations Manager с Log Analytics и продолжить обслуживание процессов и стратегии операций службы.  

## <a name="overview"></a>Обзор

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Перед анализом и обработкой собранных данных необходимо установить и подключить агенты для всех компьютеров, данные которых нужно отправить в службу Log Analytics. Вы можете установить агенты на локальных компьютерах с помощью программы установки, командной строки или DSC (настройки требуемого состояния) в службе автоматизации Azure. 

Этот агент для Linux и Windows обменивается исходящими данными со службой Log Analytics и службой автоматизации Azure через TCP-порт 443. Если компьютер подключен к брандмауэру или прокси-серверу для обмена данными через Интернет, ознакомьтесь с разделом о [настройке агента для использования с прокси-сервером или шлюзом OMS](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway), чтобы применить необходимые изменения конфигурации. Компьютер, отслеживаемый решением System Center 2016 Operations Manager или Operations Manager 2012 R2, может использоваться как многосетевой. С помощью службы Log Analytics будет выполняться сбор данных и их пересылка в службу, а компьютер по-прежнему будет отслеживаться решением [Operations Manager](log-analytics-om-agents.md). Компьютеры Linux, отслеживаемые группой управления Operations Manager, интегрированной с Log Analytics, не получают конфигурацию для источников данных. Они пересылают собранные данные через группы управления. Агент для Windows может отправлять отчет в четыре рабочих области, а агент для Linux поддерживает отчетность только для одной рабочей области.  

Агент для Linux и Windows предназначен не только для подключения к Log Analytics, в нем также поддерживается подключение к службе автоматизации Azure для размещения гибридной рабочей роли Runbook и решений управления (например, "Отслеживание изменений" и "Управление обновлениями").  Дополнительные сведения о гибридной рабочей роли Runbook см. в разделе [Общие сведения об архитектуре автоматизации](../automation/automation-offering-get-started.md#automation-architecture-overview).  

Если политики ИТ-безопасности запрещают подключение компьютеров в сети к Интернету, агент можно настроить для подключения к шлюзу OMS. Так он сможет получать сведения о конфигурации и отправлять собранные данные в зависимости от включенного решения. Дополнительные сведения и инструкции по настройке агента Linux или Windows для обмена данными со службой Log Analytics через шлюз OMS см. в статье [Connect computers without Internet access to OMS using the OMS Gateway](log-analytics-oms-gateway.md) (Подключения компьютеров к OMS с помощью шлюза OMS без доступа к Интернету). 

> [!NOTE]
> Агент для Windows поддерживает только протоколы TLS 1.0 и 1.1.  
> 

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы ознакомьтесь со следующими сведениями, чтобы выполнить следующие минимальные системные требования.

### <a name="windows-operating-system"></a>Операционная система Windows
Для агента Windows официально поддерживаются следующие версии операционной системы Windows:

* Windows Server 2008 с пакетом обновления 1 (SP1) или более поздней версии;
* Windows 7 с пакетом обновления 1 и более поздней версии.

#### <a name="network-configuration"></a>Конфигурация сети
Ниже приводятся сведения о конфигурации прокси-сервера и брандмауэра, необходимые для взаимодействия агента Windows с Log Analytics. Трафик является исходящим из локальной сети к службе Log Analytics. 

| Ресурс агента | порты; | Обход проверки HTTPS|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Yes |
|*.oms.opinsights.azure.com | 443 | Yes | 
|*.blob.core.windows.net | 443 | Yes | 
|*.azure-automation.net | 443 | Yes | 

### <a name="linux-operating-systems"></a>Операционные системы Linux
Официально поддерживаются следующие дистрибутивы Linux.  Однако агент Linux может выполняться и на других дистрибутивах, помимо следующих:  Если не указано иное, все вспомогательные версии поддерживаются для каждого основного номера версии в списке.  

* Amazon Linux 2012.09–2015.09 (x86/x64)
* CentOS Linux 5, 6 и 7 (x86/x64)  
* Oracle Linux 5, 6 и 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5, 6 и 7 (x86/x64)
* Debian GNU/Linux 6, 7 и 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 и 12 (x86/x64)

#### <a name="network-configuration"></a>Конфигурация сети
Ниже приводятся сведения о конфигурации прокси-сервера и брандмауэра, необходимые для взаимодействия агента Linux с Log Analytics.  

|Ресурс агента| порты; | Направление |  
|------|---------|--------|  
|*.ods.opinsights.azure.com | Порт 443 | Исходящий и входящий|  
|*.oms.opinsights.azure.com | Порт 443 | Исходящий и входящий|  
|*.blob.core.windows.net | Порт 443 | Исходящий и входящий|  
|*.azure-automation.net | Порт 443 | Исходящий и входящий|  

Агент Linux поддерживает обмен данными со службой Log Analytics через прокси-сервер или шлюз OMS по протоколу HTTPS.  Поддерживается анонимная и базовая аутентификация (с именем пользователя и паролем).  Прокси-сервер можно указать во время установки или при изменении файла конфигурации proxy.conf после установки.  

Значение конфигурации прокси-сервера имеет следующий синтаксис:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Если прокси-сервер не требует аутентификацию, для агента Linux все равно необходимо указать псевдо-имя пользователя или пароль. Вы можете указать любое имя пользователя или пароль.

|Свойство| ОПИСАНИЕ |
|--------|-------------|
|Протокол | HTTPS |
|user | Необязательное имя пользователя для аутентификации прокси-сервера |
|password | Необязательный пароль для аутентификации прокси-сервера |
|proxyhost | Адрес или полное доменное имя прокси-сервера или шлюза OMS |
|порт | Номер дополнительного порта для прокси сервера или OMS шлюза |

Например: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Если в пароле использованы специальные знаки (например, @), отобразится ошибка подключения прокси-сервера из-за неправильного анализа значения.  Чтобы обойти эту проблему, закодируйте пароль в URL-адрес, используя специальные инструменты (например, [URLDecode](https://www.urldecoder.org/)).  

## <a name="install-and-configure-agent"></a>Установка и настройка агента 
Подключение локальных компьютеров напрямую с помощью Log Analytics может осуществляться разными способами в зависимости от требований. В следующей таблице описан каждый метод. Эти данные помогут вам определить, какой из методов наиболее подходящий для вашей организации.

|Источник | Метод | ОПИСАНИЕ|
|-------|-------------|-------------|
| Компьютер Windows|- [Установка вручную](log-analytics-agent-windows.md)<br>- [DSC службы автоматизации Azure](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Шаблон Resource Manager с Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Установка агента Microsoft Monitoring Agent из командной строки или автоматически, например с помощью DSC службы автоматизации Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) или шаблона Azure Resource Manager, если Microsoft Azure Stack уже развернут в центре обработки данных.| 
|Компьютер Linux| [Установка вручную](log-analytics-quick-collect-linux-computer.md)|Установка агента для Linux путем вызова сценария-оболочки, размещенного в GitHub. | 
| System Center Operations Manager|[Интеграция Operations Manager с Log Analytics](log-analytics-om-agents.md) | Настройка интеграции между Operations Manager и Log Analytics для пересылки отчетов о собранных данных с компьютеров Linux и Windows в группу управления.|  

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения об источниках данных, доступных для сбора данных из операционных систем Windows или Linux, см. в статье [Data sources in Log Analytics](log-analytics-data-sources.md) (Источники данных в Log Analytics). 

* Узнайте больше об [операциях поиска по журналу](log-analytics-log-searches.md) , которые можно применять для анализа данных, собираемых из источников данных и решений. 

* Узнайте больше о [решениях](log-analytics-add-solutions.md) , которые расширяют функции службы Log Analytics и собирают данные в репозиторий OMS.