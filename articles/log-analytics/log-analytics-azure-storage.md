<properties
	pageTitle="Подключение хранилища Azure к Log Analytics | Microsoft Azure"
	description="Log Analytics использует данные с серверов в вашей локальной или облачной инфраструктуре. При генерировании системой диагностики Azure можно брать данные компьютера из хранилища Azure."
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

# Подключение службы Azure к Log Analytics

Log Analytics использует данные с серверов в вашей локальной или облачной инфраструктуре. При генерировании системой диагностики Azure можно брать данные компьютера из хранилища Azure.

Используя данные из хранилища Azure, можно выполнять быстрый поиск событий и журналов IIS для облачных служб и виртуальных машин. Чтобы получить дополнительные сведения из виртуальных машин, можно установить Microsoft Monitoring Agent.

Решения "Оценка обновлений", "Отслеживание изменений" и "Оценка SQL" поддерживают взаимодействие с Microsoft Monitoring Agent для обеспечения более глубокого понимания виртуальных машин. Если это еще не сделано, вы можете [добавить решения Log Analytics из коллекции решений](log-analytics-add-solutions.md) после входа на [портал OMS](https://www.microsoft.com/oms/).

Для виртуальных машин Azure существует два простых способа активации сбора данных на основе агента:

- на портале управления Microsoft Azure;
- с использованием PowerShell.

При использовании сбора данных журнала на основе агента необходимо настроить, какие журналы следует собирать. Это можно сделать на странице конфигурации управления журналами [портала OMS](https://www.microsoft.com/oms/).

>[AZURE.NOTE] Если служба OMS настроена для индексации данных журнала с помощью системы диагностики Azure, и агент настроен для сбора журналов, то одни и те же журналы будут индексироваться дважды. За оба источника данных будет взиматься стандартная плата. Если агент установлен, данные журналов следует собирать с помощью агента и не индексировать журналы, собранные системой диагностики Azure.

## Портал Microsoft Azure

Вы можете установить агента для OMS и подключить виртуальную машину Azure, которая выполняется на [портале Azure](https://portal.azure.com).

### Установка агента для OMS и подключение виртуальной машины к рабочей области

1.	Войдите на [портал Azure](http://portal.azure.com).
2.	Найдите и выберите **Log Analytics (OMS)**.
3.	В списке рабочих областей OMS выберите ту, к которой нужно подключить виртуальную машину Azure. ![рабочие области oms](./media/log-analytics-azure-storage/oms-connect-azure-01.png)
4.	В разделе **Управление службой Log Analytics** щелкните **Виртуальные машины**. ![виртуальные машины](./media/log-analytics-azure-storage/oms-connect-azure-02.png)
5.	В списке **виртуальных машин** ту, на которую нужно установить агент. **Состояние подключения OMS** для виртуальной машины примет значение **Не подключена**. ![виртуальная машина не подключена](./media/log-analytics-azure-storage/oms-connect-azure-03.png)
6.	Введите данные виртуальной машины и щелкните **Подключить**. Агент устанавливается и настраивается для рабочей области OMS автоматически, но обработка может занять определенное время. ![подключить виртуальную машину](./media/log-analytics-azure-storage/oms-connect-azure-04.png)
7.	Если агент установлен и подключен, состояние **подключения OMS** обновится и примет значение **Эта рабочая область**. ![подключена](./media/log-analytics-azure-storage/oms-connect-azure-05.png)

>[AZURE.NOTE] Для выполнения автоматической установки агента оперативной аналитики должен быть установлен [агент OMS](../virtual-machines/virtual-machines-windows-extensions-features.md). Если у вас есть виртуальная машина диспетчера ресурсов Azure, она не будет показана в списке. Вам следует использовать PowerShell или создать шаблон ARM для установки агента.

Виртуальные машины могут не отображаться на портале управления Azure по следующим причинам:

- Виртуальная машина уже управляется другой рабочей областью OMS
- Виртуальная машина представляет собой виртуальную машину ARM, которая на данный момент не поддерживается

## PowerShell

Если вы предпочитаете использовать сценарии, агент Microsoft Monitoring Agent можно включить с помощью PowerShell.

Microsoft Monitoring Agent — это [расширение виртуальной машины Azure](../virtual-machines/virtual-machines-windows-extensions-features.md), которым можно управлять с помощью PowerShell, как показано в следующих примерах.

Для классических виртуальных машин Azure используйте следующий пример PowerShell:

```
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```


Для виртуальных машин диспетчера ресурсов Azure используйте следующий пример PowerShell:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"


$workspaceId="**"
$workspaceKey="**"

$resourcegroup = "**"
$resourcename = "**"

$vm = Get-AzureRMVM -ResourceGroupName $resourcegroup -Name $resourcename
$location = $vm.Location

Set-AzureRMVMExtension -ResourceGroupName $resourcegroup -VMName $resourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId':  '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey' }"


```
При настройке с помощью PowerShell необходимо указать идентификатор рабочей области и первичный ключ. Идентификатор рабочей области и первичный ключ находятся на странице **Параметры** портала OMS.

![идентификатор рабочей области и первичный ключ](./media/log-analytics-azure-storage/oms-analyze-azure-sources.png)

## Сбор данных с помощью системы диагностики Azure

OMS может анализировать данные, записанные в хранилище Azure с помощью системы диагностики Azure. Необходимо выполнить два основных шага:

- настроить сбор диагностических данных в хранилище Azure;
- настроить OMS для анализа данных в учетной записи хранения.

В разделах ниже описывается, как включить сбор диагностических данных в хранилище Azure и как затем настроить OMS для анализа данных в этом хранилище.

Система диагностики Azure — это расширение Azure, которое позволяет собирать диагностические данные из рабочей роли, веб-роли или виртуальной машины, выполняющейся в Azure. Данные сохраняются в учетной записи хранения Azure и затем могут использоваться службой OMS.

>[AZURE.NOTE] Если у вас есть оплаченная подписка, за хранение и выполнение операций будет взиматься стандартная плата, если вы отправляете данные диагностики в учетную запись хранения, и OMS считывает данные из вашей учетной записи хранения.

Система диагностики Azure может собирать телеметрию следующих типов:

Источник данных|Описание
 ---|---
Журналы IIS|Информация о веб-сайтах IIS.
Журналы инфраструктуры системы диагностики Azure|Информация о самой системе диагностики.
Журналы неудачно завершенных запросов IIS |Информация о неудачно завершенных запросах, отправленных на сайт или в приложение IIS.
Журналы событий Windows|Информация, отправляемая системой ведения журналов событий Windows.
Счетчики производительности|Системные и пользовательские счетчики производительности.
Аварийные дампы|Информация о состоянии процесса в случае сбоя приложения.
Пользовательские журналы ошибок|Журналы, созданные вашим приложением или службой.
.NET EventSource|События, созданные вашим кодом с использованием [класса EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource%28v=vs.110%29.aspx) .NET.
Трассировка событий Windows на основе манифестов|События трассировки событий Windows, созданные каким-либо процессом
Syslog|События, отправляемые в управляющие программы Syslog или Rsyslog


В настоящее время OMS может анализировать:

- Журналы IIS от веб-ролей и виртуальных машин
- Журналы событий Windows от веб-ролей и рабочих ролей и виртуальных машин Azure, работающих под операционной системой Windows
- Системный журнал от виртуальных машин Azure, работающих под операционной системой Linux

Журналы должны находиться в следующих расположениях:

- WADWindowsEventLogsTable (хранилище таблиц) — содержит сведения из журналов событий Windows.
- wad-iis-logfiles (хранилище больших двоичных объектов) — содержит сведения о журналах IIS.
- LinuxsyslogVer2v0 (хранилище таблиц) — содержит события системного журнала Linux.

    >[AZURE.NOTE] Журналы IIS веб-сайтов Azure в настоящее время не поддерживаются.

При работе с виртуальными машинами вы можете установить [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) на виртуальную машину, чтобы включить дополнительные функции анализа. Это позволит, помимо возможности анализа журналов IIS и журналов событий, выполнять дополнительные виды анализа, включая отслеживание изменений конфигурации, оценку SQL и оценку обновлений.

Вы можете помочь нам определить, какие дополнительные журналы должны анализироваться службой OMS в первую очередь, проголосовав на [странице отзывов](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

## Включение диагностики в веб-роли для сбора журналов IIS и событий

См. раздел [Включение диагностики в облачной службе](https://msdn.microsoft.com/library/azure/dn482131.aspx). Используя основные сведения из указанного раздела, выполните приведенные здесь инструкции для OMS.

При включении диагностики Azure происходит следующее.

- Журналы IIS по умолчанию сохраняются, а их данные передаются с интервалом, заданным с помощью параметра scheduledTransferPeriod/

- Журналы событий Windows по умолчанию не передаются.

### Включение диагностики

Для включения журналов событий Windows или изменения scheduledTransferPeriod настройте систему диагностики Azure с помощью XML-файла конфигурации (diagnostics.wadcfg), как указано в пунктах [Шаг 2. Добавление файла diagnostics.wadcfg в решение Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) и [Шаг 3. Настройка системы диагностики для приложения](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) раздела "Включение диагностики в облачной службе". В следующем примере файл конфигурации собирает все журналы IIS и все события из журналов приложения и системы:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Выполняя пункт [Шаг 4. Настройка хранения ваших диагностических данных](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) раздела "Включение диагностики в облачной службе", убедитесь, что в параметре ConfigurationSettings указана учетная запись хранения, как в приведенном ниже примере.

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Значения **AccountName** и **AccountKey** можно найти на портале управления Microsoft Azure. Для этого откройте раздел "Управление ключами доступа" на панели мониторинга учетной записи хранения. Для строки подключения должен использоваться протокол **https**.

После того как обновленная конфигурация диагностики будет применена к облачной службе и диагностические данные начнут записываться в хранилище Azure, можно приступить к настройке OMS.

## Включение диагностики Azure в виртуальной машине для сбора журналов событий и журналов IIS

Следующая процедура используется для включения диагностики Azure в виртуальной машине для сбора журналов событий и журналов IIS с помощью портала управления Microsoft Azure.

### Включение диагностики Azure в виртуальной машине с помощью портала управления Azure

1. При создании виртуальной машины установите агент ВМ. Если виртуальная машина уже существует, проверьте, установлен ли агент.
	- Если для создания виртуальной машины используется портал управления Azure по умолчанию, выберите вариант **Настраиваемое создание** и параметр **Установить агент ВМ**.
	- Если же для создания виртуальной машины используется новый портал управления, выберите пункт **Необязательная конфигурация**, а затем **Диагностика** и задайте для параметра **Состояние** значение **Вкл**.

	В результате для виртуальной машины будет автоматически установлено и запущено расширение "Система диагностики Azure", которое будет отвечать за сбор диагностических данных.

2. На существующей виртуальной машине включите мониторинг и настройте ведение журнала событий. Диагностику можно включить на уровне ВМ. Чтобы включить диагностику, а затем настроить ведение журнала событий, выполните указанные ниже действия.
	1. Выберите виртуальную машину.
	2. Щелкните **Мониторинг**.
	3. Щелкните **Диагностика**.
	4. Установите **Состояние** в значение **ВКЛ**.
	5. Выберите все нужные диагностические метрики. OMS может анализировать системные журналы событий Windows, журналы событий приложений Windows и журналы IIS.
	7. Нажмите кнопку **ОК**.

С помощью Azure PowerShell можно более точно указать, какие события должны записываться в хранилище. Пример файла конфигурации и подробное описание его схемы можно найти в разделе "Схема конфигурации системы диагностики Azure 1.2". Установите и настройте Azure PowerShell 0.8.7 или более поздней версии, как описано в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Если установлена версия системы диагностики Microsoft Azure более ранняя чем 1.2, новый портал нельзя использовать для включения или настройки средств диагностики.

Включить и обновить агент можно с помощью приведенного ниже скрипта PowerShell. Его также можно использовать с пользовательской конфигурацией ведения журналов. Скрипт необходимо изменить, указав учетную запись хранения, имя службы и имя виртуальной машины.

### Включение диагностики в виртуальной машине с помощью Azure PowerShell

Изучите приведенный ниже образец скрипта, скопируйте, измените нужным образом и сохраните как файл скрипта PowerShell, а затем выполните скрипт.


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## Включение анализа хранилища Azure с помощью OMS

Вы можете включить анализ хранилища и настроить OMS для чтения данных из учетной записи хранения Azure с диагностикой Azure, как указано в разделе [Источники данных в службе Log Analytics](log-analytics-data-sources.md#collect-data-from-azure-diagnostics).

Приблизительно через час вы увидите, что в OMS начали поступать данные из учетной записи хранения для анализа.


## Дальнейшие действия

- Если ваша организация использует прокси-сервер или брандмауэр, [настройте прокси-сервер и брандмауэр в Log Analytics](log-analytics-proxy-firewall.md), чтобы агенты могли взаимодействовать со службой Log Analytics.

<!---HONumber=AcomDC_0504_2016-->