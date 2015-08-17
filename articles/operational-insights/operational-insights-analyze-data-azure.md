<properties
   pageTitle="Анализ данных с серверов в Microsoft Azure"
   description="Поиск событий и журналов IIS для облачных служб и виртуальных машин посредством включения диагностики Azure."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders"/>
# Анализ данных с серверов в Microsoft Azure

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights использует данные с серверов в вашей локальной или облачной инфраструктуре. При генерировании системой диагностики Azure можно брать данные компьютера из хранилища Azure.

Используя данные из хранилища Azure, можно выполнять быстрый поиск событий и журналов IIS для облачных служб и виртуальных машин. Чтобы получить дополнительные сведения из виртуальных машин, можно установить Microsoft Monitoring Agent.

Решения "Оценка обновлений", "Отслеживание изменений" и "Оценка SQL" поддерживают взаимодействие с Microsoft Monitoring Agent для обеспечения более глубокого понимания виртуальных машин. Если вы этого еще не сделали, можно [добавить решения](operational-insights-setup-workspace.md) при входе на [портал оперативной аналитики](https://www.microsoft.com/oms/).

Для виртуальных машин Azure существует два простых способа активации сбора данных на основе агента:

- на портале управления Microsoft Azure;

- с использованием PowerShell.

При использовании сбора данных журнала на основе агента необходимо настроить, какие журналы следует собирать. Это можно сделать на странице конфигурации управления журналами [портала оперативной аналитики](https://www.microsoft.com/oms/).

 >[AZURE.NOTE]Если служба Operational Insights настроена для индексации данных журнала с помощью системы диагностики Azure, и агент настроен для сбора журналов, то одни и те же журналы будут индексироваться дважды. За оба источника данных будет взиматься стандартная плата. Если агент установлен, данные журналов следует собирать с помощью агента и не индексировать журналы, собранные системой диагностики Azure.

## Портал управления Microsoft Azure

Установить агент для оперативной аналитики можно с [портала Azure](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces).

### Установка агента для службы Operational Insights
1. На портале Azure перейдите в рабочую область оперативной аналитики и откройте вкладку **Серверы**.
2. На вкладке отображается список виртуальных машин. Выберите виртуальную машину, на которую нужно установить агент, и щелкните **Включить оперативную аналитику**.

Агент будет автоматически установлен и настроен для рабочей области Operational Insights.

![Image of Operational Insights Servers page](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]Для выполнения автоматической установки агента оперативной аналитики должен быть установлен [агент ВМ Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx).



## PowerShell

Если вы предпочитаете использовать сценарии, агент Microsoft Monitoring Agent можно включить с помощью PowerShell.

Microsoft Monitoring Agent — это [расширение виртуальной машины Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx), которым можно управлять с помощью PowerShell, как показано в следующем примере.

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

При настройке с помощью PowerShell необходимо указать идентификатор рабочей области и первичный ключ. Идентификатор рабочей области и первичный ключ находятся на странице **Параметры** портала оперативной аналитики.

![Источники](./media/operational-insights-analyze-data-azure/sources01.png)

## Сбор данных с помощью системы диагностики Azure

Служба Operational Insights может анализировать данные, записанные в хранилище Azure с помощью системы диагностики Azure. Необходимо выполнить два основных шага:

- настроить сбор диагностических данных в хранилище Azure;
- настроить службу Operational Insights для анализа данных в учетной записи хранения.

В разделах ниже описывается, как включить сбор диагностических данных в хранилище Azure и как затем настроить службу Operational для анализа данных в этом хранилище.

Система диагностики Azure — это расширение Azure, которое позволяет собирать диагностические данные из рабочей роли, веб-роли или виртуальной машины, выполняющейся в Azure. Данные сохраняются в учетной записи хранения Azure и затем могут использоваться службой Operational Insights.

>[AZURE.NOTE]За хранение и выполнение операций взимается стандартная плата, если вы отправляете данные диагностики в учетную запись хранения, и служба Operational Insights считывает данные из вашей учетной записи хранения.

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
.NET EventSource|События, созданные вашим кодом с использованием класса [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx) .NET
Трассировка событий Windows на основе манифестов|События трассировки событий Windows, созданные каким-либо процессом
Syslog|События, отправляемые в управляющие программы Syslog или Rsyslog


На данный момент служба Operational Insights может анализировать:

- Журналы IIS от веб-ролей и виртуальных машин
- Журналы событий Windows от веб-ролей и рабочих ролей и виртуальных машин Azure, работающих под операционной системой Windows
- Системный журнал от виртуальных машин Azure, работающих под операционной системой Linux

Журналы должны находиться в следующих расположениях:

- WADWindowsEventLogsTable (хранилище таблиц) — содержит сведения из журналов событий Windows.
- wad-iis-logfiles (хранилище больших двоичных объектов) — содержит сведения о журналах IIS.
- LinuxsyslogVer2v0 (хранилище таблиц) — содержит события системного журнала Linux.

 >[AZURE.NOTE]Журналы IIS веб-сайтов Azure в настоящее время не поддерживаются.

При работе с виртуальными машинами имеется возможность установить [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) на виртуальную машину, чтобы включить дополнительные функции анализа. Это позволит, помимо возможности анализа журналов IIS и журналов событий, выполнять дополнительные виды анализа, включая отслеживание изменений конфигурации, оценку SQL и оценку обновлений.

Вы можете помочь нам определить, какие дополнительные журналы должны анализироваться службой оперативной аналитики в первую очередь, проголосовав на [странице отзывов](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

## Включение диагностики в веб-роли для сбора журналов IIS и событий

См. раздел [Включение диагностики в облачной службе](https://msdn.microsoft.com/library/azure/dn482131.aspx). Используя основные сведения из указанного раздела, выполните приведенные здесь инструкции для настройки Microsoft Azure Operational Insights.

При включении диагностики Azure происходит следующее.

- Журналы IIS по умолчанию сохраняются, а их данные передаются с интервалом, заданным с помощью параметра scheduledTransferPeriod/

- Журналы событий Windows по умолчанию не передаются.

### Включение диагностики

Для включения журналов событий Windows или изменения scheduledTransferPeriod настройте систему диагностики Azure с помощью XML-файла конфигурации (diagnostics.wadcfg), как указано в пунктах [Шаг 2. Добавление файла diagnostics.wadcfg в решение Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) и [Шаг 3. Настройка системы диагностики для приложения](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) раздела «Включение диагностики в облачной службе». В следующем примере файл конфигурации собирает все журналы IIS и все события из журналов приложения и системы.

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


Выполняя пункт [Шаг 4. Настройка хранения ваших диагностических данных](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) раздела «Включение диагностики в облачной службе», убедитесь, что в параметре ConfigurationSettings указана учетная запись хранения, как в приведенном ниже примере.


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Значения **AccountName** и **AccountKey** можно найти на панели мониторинга учетной записи в разделе «Управление ключами доступа» на портале управления Microsoft Azure. Для строки подключения должен использоваться протокол **https**.

После того как обновленная конфигурация диагностики будет применена к облачной службе и диагностические данные начнут записываться в хранилище Azure, можно приступить к настройке Operational Insights.

## Включение диагностики Azure в виртуальной машине для сбора журналов событий и журналов IIS

Следующая процедура используется для включения диагностики Azure в виртуальной машине для сбора журналов событий и журналов IIS с помощью портала управления Microsoft Azure.

### Включение диагностики Azure в виртуальной машине с помощью портала управления Azure

1. При создании виртуальной машины установите агент ВМ. Если виртуальная машина уже существует, проверьте, установлен ли агент.
	- Если для создания виртуальной машины используется портал управления Azure по умолчанию, выберите вариант **Настраиваемое создание** и параметр **Установить агент ВМ**.
	- Если используется новый портал управления, выберите пункт **Дополнительная настройка**, а затем **Диагностика** и задайте для параметра **Состояние** значение **Вкл**.

	В результате для виртуальной машины будет автоматически установлено и запущено расширение "Система диагностики Azure", которое будет отвечать за сбор диагностических данных.

2. На существующей виртуальной машине включите мониторинг и настройте ведение журнала событий. Диагностику можно включить на уровне ВМ. Чтобы включить диагностику, а затем настроить ведение журнала событий, выполните указанные ниже действия.
	1. Выберите виртуальную машину.
	2. Щелкните **Мониторинг**.
	3. Щелкните **Диагностика**.
	4. Установите **Состояние** в значение **ВКЛ**.
	5. Выберите все нужные диагностические метрики. Служба Operational Insights может анализировать системные журналы событий Windows, журналы событий приложений Windows и журналы IIS.
	7. Нажмите кнопку **ОК**.

С помощью Azure PowerShell можно более точно указать, какие события должны записываться в хранилище. Пример файла конфигурации и подробное описание его схемы можно найти в разделе "Схема конфигурации системы диагностики Azure 1.2". Установите и настройте Azure PowerShell 0.8.7 или более поздней версии, как описано в статье [Установка и настройка Azure PowerShell](powershell-install-configure). Если установлена версия системы диагностики Microsoft Azure более ранняя чем 1.2, новый портал нельзя использовать для включения или настройки средств диагностики.

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


## Включение анализа хранилища Azure с помощью Operational Insights

Чтобы включить анализ хранения и настроить Operational Insights для чтения данных из учетной записи хранения Azure с помощью системы диагностики Azure, выполните следующие действия.

### Включение анализа с помощью Operational Insights

1. На портале Azure по умолчанию перейдите к рабочей области оперативной аналитики и откройте вкладку **Хранилище**.![Вкладка хранилища рабочей области](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. Щелкните **Добавить учетную запись хранения**, чтобы открыть окно **Добавление учетной записи хранения**.
3. Выберите учетную запись хранения, которую вы хотите использовать.
4. В списке **Тип данных** выберите тип данных: **События**, **Журналы IIS** или **Syslog (Linux)**.
5. Щелкните значок **ОК**.<br> ![Окно учетной записи хранения](./media/operational-insights-analyze-data-azure/storage-account.png)
6. Повторите описанные выше действия для каждого сочетания типа данных и учетной записи хранения, для которого нужно выполнять сбор данных.

Приблизительно через час вы увидите, что в службу Operational Insights начали поступать данные из учетной записи хранения для анализа.

## Связанная информация

- [Прямое подключение компьютеров к Operational Insights](operational-insights-direct-agent)
- [Запись блога: включение Operational Insights для виртуальных машин Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Дальнейшие действия

[Настройка параметров прокси-сервера и брандмауэра (необязательно)](../operational-insights-proxy-filewall.md)

<!---HONumber=August15_HO6-->