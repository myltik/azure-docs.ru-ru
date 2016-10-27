<properties
    pageTitle="Использование хранилища BLOB-объектов для IIS и хранилища таблиц для событий | Microsoft Azure"
    description="Log Analytics может считывать журналы служб Azure, которые записывают диагностические данные в хранилище таблиц, или журналы IIS, записанные в хранилище BLOB-объектов."
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
    ms.date="10/10/2016"
    ms.author="banders"/>



# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Использование хранилища BLOB-объектов для IIS и хранилища таблиц для событий

Log Analytics может считывать журналы следующих служб, которые записывают диагностические данные в хранилище таблиц, или журналы IIS, записанные в хранилище BLOB-объектов:

+ кластеры Service Fabric (предварительная версия);
+ Виртуальные машины
+ Веб-роль или рабочая роль.

Чтобы служба Log Analytics могла собирать данные для этих ресурсов, необходимо включить систему диагностики Azure.

После включения диагностики можно использовать портал Azure или PowerShell, чтобы настроить сбор журналов в Log Analytics.

Система диагностики Azure — это расширение Azure, которое позволяет собирать диагностические данные из рабочей роли, веб-роли или виртуальной машины, выполняющейся в Azure. Данные сохраняются в учетной записи хранения Azure и затем могут собираться Log Analytics.

Log Analytics может собирать эти журналы системы диагностики Azure, если они находятся в следующих расположениях:

| Тип журнала | Тип ресурса | Расположение |
| -------- | ------------- | -------- |
| Журналы IIS | Виртуальные машины <br> Веб-роли <br> Рабочие роли | wad-iis-logfiles (хранилище BLOB-объектов) |
| syslog | Виртуальные машины | LinuxsyslogVer2v0 (хранилище таблиц) |
| Операционные события Service Fabric | Узлы Service Fabric | WADServiceFabricSystemEventTable |
| События субъектов Service Fabric Reliable Actors | Узлы Service Fabric | WADServiceFabricReliableActorEventTable |
| События надежных служб Service Fabric | Узлы Service Fabric | WADServiceFabricReliableServiceEventTable |
| Журналы событий Windows | Узлы Service Fabric <br> Виртуальные машины <br> Веб-роли <br> Рабочие роли | WADWindowsEventLogsTable (хранилище таблиц) |
| Журналы трассировки событий Windows | Узлы Service Fabric <br> Виртуальные машины <br> Веб-роли <br> Рабочие роли | WADETWEventTable (хранилище таблиц) |

>[AZURE.NOTE] Журналы IIS веб-сайтов Azure в настоящее время не поддерживаются.

При работе с виртуальными машинами вы можете установить [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) на виртуальную машину, чтобы включить дополнительные функции анализа. Это позволит, помимо возможности анализа журналов IIS и журналов событий, выполнять дополнительные виды анализа, включая отслеживание изменений конфигурации, оценку SQL и оценку обновлений.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Включение диагностики Azure в виртуальной машине для сбора журналов событий и журналов IIS

Следующая процедура используется для включения диагностики Azure в виртуальной машине для сбора журналов событий и журналов IIS с помощью портала управления Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-management-portal"></a>Включение диагностики Azure в виртуальной машине с помощью портала управления Azure

1. При создании виртуальной машины установите агент ВМ. Если виртуальная машина уже существует, проверьте, установлен ли агент.
    - На портале управления Azure перейдите к виртуальной машине, выберите пункт **Необязательная настройка**, а затем **Диагностика** и задайте для параметра **Состояние** значение **Вкл.**

    В результате для виртуальной машины будет автоматически установлено и запущено расширение "Система диагностики Azure", которое будет отвечать за сбор диагностических данных.

2. На существующей виртуальной машине включите мониторинг и настройте ведение журнала событий. Диагностику можно включить на уровне ВМ. Чтобы включить диагностику, а затем настроить ведение журнала событий, выполните указанные ниже действия.
    1. Выберите виртуальную машину.
    2. Щелкните **Мониторинг**.
    3. Щелкните **Диагностика**.
    4. Установите для параметра **Состояние** значение **ВКЛ.**.
    5. Выберите все нужные журналы диагностики.
    7. Нажмите кнопку **ОК**.

С помощью Azure PowerShell можно более точно указать, какие события должны записываться в хранилище. См. статью [Collect data using Azure diagnostics written to table storage or IIS Logs written to blob](log-analytics-azure-storage-json.md) (Сбор данных с помощью системы диагностики Azure с записью в хранилище таблиц или с помощью журналов IIS с записью в большой двоичный объект).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Включение диагностики в веб-роли для сбора журналов IIS и событий

См. статью [Включение диагностики в облачной службе](../cloud-services/cloud-services-dotnet-diagnostics.md). Используя основные сведения из указанного раздела, выполните приведенные здесь инструкции для Log Analytics.

При включении диагностики Azure происходит следующее.

- Журналы IIS по умолчанию сохраняются, а их данные передаются с интервалом, заданным с помощью параметра scheduledTransferPeriod/
- Журналы событий Windows по умолчанию не передаются.

### <a name="to-enable-diagnostics"></a>Включение диагностики

Чтобы включить журналы событий Windows или изменить значение scheduledTransferPeriod, настройте диагностику с помощью XML-файла конфигурации (diagnostics.wadcfg), как описывается в разделе [Шаг 4. Создание файла конфигурации системы диагностики и установка расширения](../cloud-services/cloud-services-dotnet-diagnostics.md)

В следующем примере файл конфигурации собирает все журналы IIS и все события из журналов приложения и системы:

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

Убедитесь, что в параметре ConfigurationSettings указана учетная запись хранения, как в следующем примере:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Значения **AccountName** и **AccountKey** можно найти на портале управления Azure. Для этого откройте раздел "Управление ключами доступа" на панели мониторинга учетной записи хранения. Для строки подключения должен использоваться протокол **https**.

После того как обновленная конфигурация диагностики будет применена к облачной службе и диагностические данные начнут записываться в хранилище Azure, можно приступить к настройке Log Analytics.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Сбор журналов из хранилища Azure с помощью портала Azure

Чтобы настроить сбор журналов для следующих служб Azure в Log Analytics, можно использовать портал Azure:

+ Кластеры Service Fabric.
+ Виртуальные машины
+ Веб-роль или рабочая роль.

На портале Azure перейдите к рабочей области Log Analytics и сделайте следующее:

1. Щелкните *Storage accounts logs* (Журналы учетных записей хранения).
2. Щелкните задачу *Добавить*.
3. Выберите учетную запись, содержащую журналы диагностики.
  - Это может быть классическая учетная запись хранения или учетная запись хранения Azure Resource Manager.
4. Выберите тип данных, по которому требуется собирать журналы.
  - Это будет один из таких типов: журналы IIS, события, системный журнал (Linux), журналы трассировки событий Windows, события Service Fabric.
5. Значение параметра "Источник" заполняется автоматически на основе типа данных и его нельзя изменить.
6. Нажмите кнопку "ОК", чтобы сохранить конфигурацию.

Повторите шаги 2–6 для дополнительных учетных записей хранения и типов данных, которые нужно собирать в Log Analytics.

Приблизительно через полчаса в Log Analytics отобразятся данные из учетной записи хранения. Отобразятся только данные, записанные в хранилище после применения конфигурации. Log Analytics не считывает уже существующие данные из учетной записи хранения.

>[AZURE.NOTE] Портал не проверяет существование источника, а также то, записываются ли новые данные.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Включение диагностики Azure на виртуальной машине для сбора журналов событий и журналов IIS с помощью PowerShell

С помощью Azure PowerShell можно более точно указать, какие события должны записываться в хранилище.
Дополнительные сведения см. в статье [Включение диагностики на виртуальных машинах Azure](../virtual-machines-dotnet-diagnostics.md).

Включить и обновить агент можно с помощью приведенного ниже скрипта PowerShell.
Его также можно использовать с пользовательской конфигурацией ведения журналов.
Сценарий необходимо изменить, указав учетную запись хранения, имя службы и имя виртуальной машины.
Сценарий использует командлеты для классических виртуальных машин.

Изучите приведенный ниже образец скрипта, скопируйте, измените нужным образом и сохраните как файл скрипта PowerShell, а затем выполните скрипт.

```
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
```

## <a name="next-steps"></a>Дальнейшие действия

- [Используйте JSON-файлы в хранилище BLOB-объектов](log-analytics-azure-storage-json.md) для чтения журналов из служб Azure, которые записывают диагностику в хранилище BLOB-объектов в формате JSON.
- [Включите решения](log-analytics-add-solutions.md) , чтобы обеспечить глубокое понимание данных.
- [Воспользуйтесь запросами поиска](log-analytics-log-searches.md) для анализа данных.



<!--HONumber=Oct16_HO2-->


