<properties 
   pageTitle="Сбор данных компьютеров" 
   description="Сбор данных компьютеров с серверов в локальной или облачной инфраструктуре" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
#Сбор данных компьютеров

Operational Insights использует данные с серверов в локальной или облачной инфраструктуре. Данные компьютеров можно собирать из следующих источников:

- агенты Operations Manager;
- компьютеры, подключенные непосредственно к Operational Insights;
- диагностические данные виртуальных машин в службах Azure Storage.

Собранные данные отправляются в службу Operational Insights.

## Подключение к Operational Insights из System Center Operations Manager 

Службу Operational Insights можно подключить к существующей среде System Center Operations Manager. Это позволит использовать существующие агенты Operations Manager в качестве агентов службы Operational Insights. Дополнительные сведения об использовании Operations Manager с Operational Insights см. в разделе [Факторы, которые необходимо учитывать при использовании Operations Manager с Operational Insights](operational-insights-operations-manager.md).

Если Operations Manager используется для мониторинга следующих рабочих нагрузок, для них потребуется задать учетные записи запуска от имени в Operations Manager. Дополнительные сведения о настройке учетных записей запуска от имени см. разделе [Учетные записи запуска от имени в Operations Manager для Operational Insights](operational-insights-run-as.md).

- Оценка SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] Поддержка службы Operational Insights доступна в Operations Manager 2012 с пакетом обновления 1 (SP1) и с накопительным пакетом обновления 6 (UR6) и в Operations Manager 2012 R2 с накопительным пакетом обновления 2 (UR2). Поддержка прокси-сервера была добавлена в System Center Operations Manager 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 7 (UR7) и в System Center Operations Manager 2012 R2 с накопительным пакетом обновления 3(UR3).

#### Подключение Operations Manager к Operational Insights и добавление агентов

1. В консоли Operations Manager щелкните **Администрирование**.

2. Разверните узел **Operational Insights** и щелкните **Подключение к Operational Insights**.

3. Щелкните ссылку **Зарегистрироваться в Operational Insights** и следуйте инструкциям на экране. 

4. После завершения работы мастера регистрации нажмите кнопку **Добавить компьютер или группу**.

5. В диалоговом окне **Поиск компьютера** можно выполнить поиск компьютеров или групп, отслеживаемых с помощью Operations Manager. Выберите компьютеры или группы для использования в службе Operational Insights, нажмите кнопку **Добавить**, а затем - кнопку **ОК**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Прямое подключение компьютеров к Operational Insights 

Компьютеры можно напрямую подключить к Operational Insights, установив на них агент Operational Insights. 


###Загрузка и установка агента

####Загрузка файла установки агента
1. На **портале Operations Insights** на странице **Обзор** щелкните **Серверы и использование**.
1. В разделе **Напрямую подключенные серверы** щелкните **Настроить**.
1. Рядом с полем **Добавить агентов** щелкните ссылку на агент, чтобы загрузить файл установки.
1. В поле **Первичный ключ рабочей области** выберите ключ и скопируйте его (CTRL+C).


#### Установка агента с помощью программы установки
1. Запустите программу установки, чтобы установить агент на компьютер, которым требуется управлять.
1. Выберите **Подключить агент к Microsoft Azure Operational Insights**, а затем нажмите кнопку **Далее**.
1. При появлении запроса введите данные, скопированные на шаге 4.
1. По завершении **Microsoft Management Agent** появится на **панели управления**.

#### Установка агента с помощью командной строки
Измените, а затем используйте следующий пример для установки агента с помощью командной строки.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Перенастройка агента Microsoft Monitoring Agent (необязательно)
Используйте следующие сведения, чтобы включить агент для прямого взаимодействия со службой Microsoft Azure Operational Insights. После настройки агент будет зарегистрирован в службе агента и получит необходимые данные конфигурации и пакеты управления, содержащие сведения о пакете аналитики.

После сбора данных с компьютеров, отслеживаемых агентом, количество этих компьютеров появится на портале Operational Insights на странице "Использование" в разделе **Напрямую подключенные агенты**. На портале Operational Insights можно просмотреть данные и сведения об оценке любого компьютера, который отправляет данные.

При необходимости агент можно отключить или включить с помощью командной строки или скрипта.

#### Настройка агента
- После установки **Microsoft Monitoring Agent** откройте **панель управления**.
- Откройте агент Microsoft Monitoring Agent и перейдите на вкладку Azure Operational Insights.
- Выберите **Подключение к Azure Operational Insights**.
- В поле **Идентификатор рабочей области** введите идентификатор рабочей области, указанный на портале службы Operational Insights.
- В поле "Ключ учетной записи" вставьте **первичный ключ рабочей области**, скопированный при установке агента, а затем нажмите кнопку **OK**.

#### Отключение агента
- После установки агента откройте **панель управления**.
- Откройте агент Microsoft Monitoring Agent и перейдите на вкладку **Azure Operational Insights**.
- Снимите флажок **Подключение к Azure Operational Insights**.

#### Включение агента с помощью командной строки или скрипта
В следующем примере можно использовать Windows PowerShell или скрипт VB.

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Настройка параметров прокси-сервера и брандмауэра (необязательно)
Если в вашей среде используются прокси-серверы или брандмауэры, ограничивающие доступ к Интернету, может потребоваться выполнить следующие процедуры, чтобы Operations Manager и (или) агенты могли подключаться к службе Operational Insights. 



- [Настройка параметров прокси-сервера и брандмауэра (необязательно)](operational-insights-proxy-filewall.md) 

## Анализ данных с серверов в Microsoft Azure

Служба Operational Insights позволяет быстро выполнять поиск событий и журналов IIS для облачных служб и виртуальных машин путем включения [системы диагностики в облачных службах Azure](operational-insights-log-collection.md). Чтобы получить дополнительные сведения из виртуальных машин, можно установить Microsoft Monitoring Agent.

Пакеты аналитики "Оценка обновлений", "Отслеживание изменений" и "Оценка SQL" поддерживают взаимодействие с Microsoft Monitoring Agent для обеспечения более глубокого понимания виртуальных машин. Эти пакеты аналитики можно включить на [портале Operational Insights](https://preview.opinsights.azure.com/).

Для виртуальных машин Azure существует два простых способа активации сбора данных на основе агента:

- на портале управления Microsoft Azure;

- с использованием PowerShell.

При использовании сбора данных журнала на основе агента необходимо настроить, какие журналы следует собирать. Это можно сделать на странице конфигурации управления журналами на [портале Operational Insights](https://preview.opinsights.azure.com/).

Если служба Operational Insights настроена для индексации данных журнала с помощью средств диагностики в облачных службах Azure и агент настроен для сбора журналов, в одних и тех же журналах событий будет по 2 копий индексированных данных. Если агент установлен, данные журналов следует собирать с помощью агента и не индексировать журналы, собранные средствами диагностики в облачных службах Azure.

### Портал управления Microsoft Azure

На [портале Operational Insights](https://preview.opinsights.azure.com/), перейдите в рабочую область Operational Insights и откройте вкладку "Серверы". На вкладке отображается список виртуальных машин. Выберите виртуальную машину, на которую нужно установить агент и щелкните **Включить Op Insights**.

Агент будет установлен и настроен для рабочей области Operational Insights.

![Image of Operational Insights Servers page](./media/operational-insights-collect-data/servers.png)

### PowerShell

Если вы предпочитаете использовать сценарии, агент Microsoft Monitoring Agent можно включить с помощью PowerShell.

Агент Microsoft Monitoring Agent - это [расширение виртуальной машины Azure](https://msdn.microsoft.com/library/azure/dn832621.aspx) которым можно управлять с помощью Windows PowerShell, как показано в следующем примере.

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

При настройке с помощью PowerShell необходимо указать идентификатор рабочей области и ключ рабочей области. Идентификатор рабочей области и ключ рабочей области находятся на странице "Серверах и использование" на портале Operational Insights.

![Image of Operational Insights Direct Agent Config](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Сбор данных с помощью системы диагностики в облачных службах Azure

Служба Operational Insights может анализировать данные, записываемые системой диагностики Azure в хранилище Azure. Необходимо выполнить два основных шага:

- настроить сбор диагностических данных в хранилище Azure;
- настроить службу Operational Insights для анализа данных в учетной записи хранения.

В разделах ниже описывается, как включить сбор диагностических данных в хранилище Azure и как затем настроить службу Operational для анализа данных в этом хранилище.

Система диагностики Azure - это расширение Azure, которое позволяет собирать диагностические данные телеметрии из рабочей роли, веб-роли или виртуальной машины, выполняющейся в Azure. Данные телеметрии сохраняются в учетной записи хранения Azure и могут затем использоваться службой Operational Insights.

>[AZURE.NOTE] При отправке диагностических данных в учетную запись хранения действуют обычные тарифы на хранение и передачу данных.

Система диагностики Azure может собирать телеметрию следующих типов:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Источник данных</b></td>
		<td><b>

Описание</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Журналы IIS</td>
		<td>Информация о веб-сайтах IIS.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Журналы инфраструктуры системы диагностики Azure</td>
		<td>Информация о самой системе диагностики.</td>
    </tr>
	<tr align="left" valign="top">
		<td>Журналы неудачно завершенных запросов IIS </td>
		<td>Информация о неудачно завершенных запросах, отправленных на сайт или в приложение IIS.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Журналы событий Windows</td>
		<td>Информация, отправляемая системой ведения журналов событий Windows.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Счетчики производительности</td>
		<td>Системные и пользовательские счетчики производительности.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Аварийные дампы</td>
		<td>Информация о состоянии процесса в случае сбоя приложения.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Пользовательские журналы ошибок</td>
		<td>Журналы, созданные вашим приложением или службой.</td>
    </tr>
    <tr align="left" valign="top">
		<td>.NET EventSource</td>
		<td>События, создаваемые кодом с помощью класса .NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource/a></td>
    </tr>
    <tr align="left" valign="top">
		<td>Трассировка событий Windows на основе манифестов</td>
		<td>События трассировки событий Windows, созданные каким-либо процессом.</td>
    </tr>
    </tbody>
    </table>


В настоящее время служба Operational Insights может анализировать журналы IIS, полученные из веб-ролей, и журналы событий Windows, полученные из веб-ролей, рабочих ролей и виртуальных машин Azure. Журналы должны находиться в следующих расположениях:

- WADWindowsEventLogsTable (табличное хранилище) - содержит сведения из журналов событий Windows.

- wad-iis-logfiles (хранилище BLOB-объектов) - содержит сведения о журналах IIS.

При работе с виртуальными машинами агент [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) также можно установить на виртуальную машину. Это позволит анализировать журналы IIS и журналы событий, а также проводить дополнительный анализ, включая отслеживание изменений конфигурации и оценку обновлений. Чтобы включить дополнительный анализ, установите [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269).

>[AZURE.NOTE] Журналы IIS веб-сайтов Azure в настоящее время не поддерживаются.

Вы можете помочь нам определить, какие дополнительные журналы должны анализироваться службой Operational Insights в первую очередь, проголосовав на [странице отзывов](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy).

### Включение диагностики в веб-роли для сбора журналов IIS и событий

См. раздел [Включение диагностики в облачной службе](https://msdn.microsoft.com/library/azure/dn482131.aspx). Используя основные сведения из указанного раздела, выполните приведенные здесь инструкции для настройки Microsoft Azure Operational Insights.

При включении диагностики Azure происходит следующее.

- Журналы IIS по умолчанию сохраняются, а их данные передаются с интервалом, заданным с помощью параметра scheduledTransferPeriod/

- Журналы событий Windows по умолчанию не передаются.

#### Включение диагностики

Чтобы включить журналы событий Windows или изменить значение scheduledTransferPeriod, настройте диагностику с помощью XML-файла конфигурации (diagnostics.wadcfg), как описывается в подразделах [Шаг 2. Добавление файла diagnostics.wadcfg в решение Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) и [Шаге 3. Настройка диагностики для своего приложения](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) раздела "Включение диагностики в облачной службе". Приведенный ниже пример файла конфигурации обеспечивает сбор журналов IIS и всех событий из журналов приложений и системного журнала.

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


При выполнении процедуры [Шаг 4. Настройка хранилища для диагностических данных](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) в разделе "Включение диагностики в облачной службе" укажите в элементе ConfigurationSettings учетную запись хранения, как в приведенном ниже примере.


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


Значения **AccountName** и **AccountKey** можно найти на панели мониторинга учетной записи в разделе "Управление ключами доступа" на портале управления Azure. Для строки подключения должен использоваться протокол **https**.

После того как обновленная конфигурация диагностики будет применена к облачной службе и диагностические данные начнут записываться в хранилище Azure, можно приступить к настройке Operational Insights.

### Включение диагностики облачных служб Azure в виртуальной машине для сбора журналов событий и журналов IIS

Следующая процедура используется для включения диагностики облачных служб Azure в виртуальной машине для сбора журналов событий и журналов IIS с помощью портала управления Microsoft Azure.

#### Включение диагностики облачных служб Azure в виртуальной машине с помощью портала управления Azure

1. При создании виртуальной машины установите агент ВМ. Если виртуальная машина уже существует, проверьте, установлен ли агент.
	- Если для создания виртуальной машины используется портал управления Azure по умолчанию, выберите вариант **Настраиваемое создание** и выберите параметр **Установить агент ВМ**.
	- Если используется новый портал управления, выберите пункт **Дополнительная настройка**, затем - **Диагностика** и задайте для параметра **Состояние** значение **Вкл.**.
	
	В результате для виртуальной машины будет автоматически установлено и запущено расширение "Система диагностики Azure", которое будет отвечать за сбор диагностических данных.

2. На существующей виртуальной машине включите мониторинг и настройте ведение журнала событий. Диагностику можно включить на уровне ВМ. Чтобы включить диагностику, а затем настроить ведение журнала событий, выполните указанные ниже действия.
	1. Выберите виртуальную машину.
	2. Щелкните **Мониторинг**.
	3. Выберите пункт **Диагностика**.
	4. Установите **Состояние** в значение **Вкл**.
	5. Выберите все нужные диагностические метрики. Служба Operational Insights может анализировать системные журналы событий Windows, журналы событий приложений Windows и журналы IIS.
	7. Нажмите кнопку **ОК**.

С помощью Azure PowerShell можно более точно указать, какие события должны записываться в хранилище. Пример файла конфигурации и подробное описание его схемы можно найти в разделе "Схема конфигурации системы диагностики Azure 1.2". Установите и настройте Azure PowerShell версии 0.8.7 или более поздней, как описано в статье [Установка и настройка Azure PowerShell](install-configure-powershell/). Если установлена версия системы диагностики Microsoft Azure Diagnostics более ранняя чем 1.2, новый портал нельзя использовать для включения или настройки средств диагностики.

Включить и обновить агент можно с помощью приведенного ниже скрипта PowerShell. Его также можно использовать с пользовательской конфигурацией ведения журналов. Скрипт необходимо изменить, указав учетную запись хранения, имя службы и имя виртуальной машины.

#### Включение диагностики в виртуальной машине с помощью Azure PowerShell

Изучите приведенный ниже образец скрипта, скопируйте, измените нужным образом и сохраните как файл скрипта PowerShell, а затем выполните скрипт.

    # Connect to Azure
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
    

### Включение анализа хранилища Azure с помощью Operational Insights

На портале Azure по умолчанию перейдите к рабочей области Operational Insights и откройте вкладку **Хранилище**. Чтобы настроить Operational Insights для чтения данных из учетной записи хранения с помощью системы диагностики, выполните указанные ниже действия.

#### Включение анализа с помощью Operational Insights

1. Нажмите кнопку **Добавить**, чтобы открыть окно **Добавление учетной записи хранения**.

2. Выберите **учетную запись хранения**.

3. Выберите тип данных: **События** или **Журналы IIS**.

4. Нажмите кнопку **ОК**.

5. Повторите описанные выше действия для каждого сочетания типа данных и учетной записи хранения, для которого нужно выполнять сбор данных.

Приблизительно через час вы увидите, что в службу Operational Insights начали поступать данные из учетной записи хранения для анализа.




## Похожий контент

- [Запись блога: Прямое подключение серверов к Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Запись блога: Включение Operational Insights для виртуальных машин Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## Дальнейшие действия

[Настройка параметров прокси-сервера и брандмауэра (необязательно)](operational-insights-proxy-filewall.md)



<!--HONumber=52-->