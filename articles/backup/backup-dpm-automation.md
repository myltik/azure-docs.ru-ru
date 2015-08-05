<properties
	pageTitle="Служба архивации Azure — развертывание резервного копирования для DPM и управление им с помощью Azure PowerShell | Microsoft Azure"
	description="Узнайте о том, как развернуть службу архивации Azure для Data Protection Manager (DPM) и управлять ей с помощью Azure PowerShell"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2015"
	ms.author="jimpark"/>


# Развертывание резервного копирования в Azure для серверов Data Protection Manager (DPM) и управление им с помощью Azure PowerShell
В этой статье описано, как использовать Azure PowerShell для настройки службы архивации Azure на сервере DPM и для управления резервным копированием и восстановлением данных.

## Настройка среды Azure PowerShell
Прежде чем использовать Azure PowerShell для управления резервными копиями Data Protection Manager в Azure, необходимо создать подходящую среду в Azure PowerShell. В начале сеанса Azure PowerShell обязательно выполните следующую команду для импорта необходимых модулей, которая также позволяет правильно ссылаться на командлеты DPM:

```
PS C:\> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command Only DPM cmdlets: Get-DPMCommand Get general help: help Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -? Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax Sample DPM scripts: Get-DPMSampleScript
```

## Настройка и регистрация
### Установка агента службы архивации Azure на сервер DPM
Прежде чем устанавливать агент службы архивации Azure, необходимо загрузить установщик и разместить его в системе Windows Server. Последнюю версию установщика можно загрузить в [Центре загрузки Майкрософт](http://aka.ms/azurebackup_agent). Сохраните установщик в удобном для вас месте, например в папке *C:\Downloads*.

Чтобы установить агент, в консоли Azure PowerShell с повышенными привилегиями **на сервере DPM** выполните следующую команду:

```
PS C:\> MARSAgentInstaller.exe /q
```

Агент будет установлен с параметрами по умолчанию. Установка займет всего несколько минут и пройдет в фоновом режиме. Если параметр */nu* не будет указан, в конце установки откроется окно **Обновления Windows** для проверки наличия обновлений.

Агент появится в списке установленных программ. Чтобы просмотреть список установленных программ, перейдите в **Панель управления** > **Программы** > **Программы и компоненты**.

![Агент установлен](./media/backup-dpm-automation/installed-agent-listing.png)

#### Параметры установки
Чтобы просмотреть все доступные в командной строке параметры, используйте следующую команду:

```
PS C:\> MARSAgentInstaller.exe /?
```

Доступны следующие параметры.

| Параметр | Сведения | значение по умолчанию |
| ---- | ----- | ----- |
| /q | Тихая установка | - |
| /p:"местоположение" | Путь к папке установки агента службы архивации Azure. | C:\\Program Files\\Microsoft Azure Recovery Services Agent | | /s:"местоположение" | Путь к папке кэша агента службы архивации Azure. | C:\\Program Files\\Microsoft Azure Recovery Services Agent\\Scratch |
| /m | Согласиться на получение обновлений от Майкрософт | - |
| /nu | Не проверять наличие обновлений после завершения установки | - |
| /d | Удаляет агент служб восстановления Microsoft Azure | - |
| /ph | Адрес узла прокси-сервера | - |
| /po | Номер порта узла прокси-сервера | - |
| / pu | Имя пользователя узла прокси-сервера | - |
| /pw | Пароль прокси-сервера | - |

### Регистрация в службе архивации Azure
Перед регистрацией в службе резервного копирования Azure убедитесь, что соблюдены [необходимые условия](backup-azure-dpm-introduction.md). Необходимо следующее:

- Действующая подписка на Azure
- Создание хранилища архивации
- Скачайте учетные данные хранилища и сохраните их в удобном расположении (например, в папке *C:\Downloads*). Для удобства вы можете изменить учетные данные хранилища.
Регистрация компьютера в хранилище выполняется с помощью командлета [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787):

```
PS C:\> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\Downloads\REGISTER.VaultCredentials"
```

Как видно, сервер с именем «TestingServer» регистрируется в хранилище Microsoft Azure на основании указанных учетных данных хранилища.

> [AZURE.IMPORTANT]Не используйте относительные пути для указания файла с учетными данными хранилища. Укажите абсолютный путь в качестве входных данных командлета.

### Исходные параметры конфигурации
После регистрации сервера DPM в хранилище службы архивации Azure он будет запущен с параметрами подписки, используемыми по умолчанию. Эти параметры подписки включают настройки сети, шифрования и промежуточной области. Чтобы приступить к изменению параметров подписки, сначала получите дескриптор существующих параметров (по умолчанию) с помощью командлета [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793):

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Все изменения вносятся в этот локальный объект ```$setting``` Azure PowerShell, а затем полный объект фиксируется в DPM и сохраняется в службе архивации Azure с помощью командлета [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). Чтобы гарантировать, что изменения будут сохранены, необходимо использовать флаг ```–Commit```. Параметры не будут применяться и использоваться службой архивации Azure до тех пор, пока они не будут зафиксированы.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

### Сеть
Если компьютер DPM подключен к службе архивации Azure в Интернете через прокси-сервер, то для успешного резервного копирования следует указать параметры прокси-сервера. Для этого используются параметры ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` и ```ProxyPassword``` для командлета [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791). В нашем случае прокси-сервер не используется, поэтому мы явным образом удаляем все данные прокси-сервера.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Управлять использованием пропускной способности для выбранных дней недели можно с помощью параметров ```-WorkHourBandwidth``` и ```-NonWorkHourBandwidth``` В данном случае мы не будем настраивать регулирование.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

### Настройка промежуточной области
Для агента службы архивации Azure, работающего на сервере DPM, необходимо предоставить временное хранилище для восстановленных из облака данных (локальная промежуточная область). Настройте промежуточную область с помощью командлета [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) с параметром ```-StagingAreaPath```.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

В примере выше задается промежуточная область *C:\StagingArea* в объекте ```$setting``` Azure PowerShell. Убедитесь, что указанная папка уже существует, иначе финальная фиксация параметров подписки завершится ошибкой.


### Параметры шифрования
Для защиты конфиденциальности данных резервные копии данных, отправляемые в службу архивации Azure, зашифровываются. Используемая для шифрования парольная фраза является «паролем» для расшифровки данных во время их восстановления. После создания фразы надежно сохраните ее и никому не сообщайте о ней.

В приведенном ниже примере первая команда преобразует строку ```passphrase123456789``` в защищенную строку и присваивает защищенную строку переменной с именем ```$Passphrase```. Вторая команда задает защищенную строку в ```$Passphrase``` в качестве пароля для шифрования резервных копий.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [AZURE.IMPORTANT]После создания парольной фразы надежно сохраните ее и никому не сообщайте о ней. Восстановить данные из Azure без парольной фразы невозможно.

На этом этапе следует внести все необходимые изменения в объект ```$setting```. Не забудьте зафиксировать изменения.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## Защита данных в службе архивации Azure
В этом разделе мы добавим рабочий сервер в DPM, а затем включим защиту данных в локальном хранилище DPM и службе архивации Azure. С помощью примеров мы покажем, как создавать резервные копии файлов и папок. Таким же способом можно создавать резервные копии любых поддерживаемых DPM источников данных. Все резервные копии DPM находятся под управлением группы защиты (ГЗ), которая состоит из четырех частей.

1. **Члены группы защиты** — это список всех защищаемых объектов (также называемых *источниками данных* в DPM), которые вы хотите защитить в рамках одной группы защиты. Например, вы можете защитить рабочие виртуальные машины в одной группе защиты, а базы данных SQL Server — в другой группе защиты, так как у них могут быть разные требования к резервному копированию. Перед созданием резервных копий данных на рабочем сервере убедитесь, что на рабочем сервере установлен агент DPM, который управляется с помощью DPM. Выполните шаги по [установке агента DPM](https://technet.microsoft.com/library/bb870935.aspx) и связыванию его с соответствующим сервером DPM.
2. **Метод защиты данных** — определяет расположения резервных копий (магнитная лента, диск и облако). В нашем примере мы организуем защиту данных на локальный диск и в облако.
3. **Расписание резервного копирования** — указывает, когда необходимо выполнять резервное копирование и как часто следует синхронизировать данные между сервером DPM и рабочим сервером.
4. **Расписание хранения** — определяет период хранения точек восстановления в Azure.

### Создание группы защиты
Начните с создания новой группы защиты с помощью командлета [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722).

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Указанный выше командлет создает группу защиты с именем *ProtectGroup01*. Существующую группу защиты можно изменить позднее, чтобы добавить резервную копию в облако Azure. Тем не менее, чтобы получить возможность вносить изменения в группу защиты (новую или существующую), необходимо получить дескриптор *изменяемого* объекта с помощью командлета [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713).

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### Добавление членов группы в группу защиты
Каждому агенту DPM известен список источников данных на сервере, на котором он установлен. Чтобы добавить источник данных в группу защиты, агент DPM должен сначала отправить список источников данных обратно на сервер DPM. Затем выбирается один или несколько источников данных, которые добавляются в группу защиты. Ниже представлены действия, которые необходимо выполнить в Azure PowerShell, чтобы добавить членов группы в группу защиты.

1. Получите список всех серверов, управляемых DPM через агент DPM.
2. Выберите определенный сервер.
3. Получите список всех источников данных на сервере.
4. Выберите один или несколько источников данных и добавьте их в группу защиты.

Чтобы получить список серверов, на которых установлен агент DPM и которые управляются сервером DPM, воспользуйтесь командлетом [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600). В этом примере мы отфильтруем и настроим для резервного копирования только сервер с именем *productionserver01*.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”
```

Теперь получим список источников данных на ```$server``` с помощью командлета [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605). В этом примере мы применяем фильтрацию для тома *D: *, который нужно настроить для резервного копирования. Затем этот источник данных добавляется в группу защиты с помощью командлета [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732). Не забывайте использовать *изменяемый* объект группы защиты ```$MPG```, чтобы вносить дополнения.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

При необходимости повторите этот шаг несколько раз, пока все выбранные источники данных не будут добавлены в группу защиты. Для начала можно выполнить рабочий процесс создания группы защиты только для одного источника данных, а затем позже добавить в эту группу дополнительные источники данных.

### Выбор способа защиты данных
После добавления в группу защиты источников данных необходимо указать метод защиты, воспользовавшись для этого командлетом [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725). В этом примере группа защиты будет настроена для резервного копирования на локальный диск и в облако.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Настройка диапазона хранения
Задайте период хранения для точек резервного копирования с помощью командлета [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762). Несмотря на то что задание периода хранения до определения расписания резервного копирования может показаться странным, командлет ```Set-DPMPolicyObjective``` автоматически задает расписание резервного копирования по умолчанию, которое в последствии можно изменить. Вы всегда можете сначала определить расписание резервного копирования, а затем задать политику хранения.

В примере ниже командлет задает параметры хранения для резервного копирования на диск. Резервные копии будут храниться 10 дней, а данные будут синхронизироваться между рабочим сервером и сервером DPM каждые 6 часов. ```SynchronizationFrequencyMinutes``` определяет не частоту создания точек резервного копирования, а то, как часто данные копируются на сервер DPM; это предотвращает создание резервных копий слишком большого размера.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Для резервных копий, сохраняемых в Azure (в DPM это называется Online Backup), диапазоны хранения можно настроить для [долгосрочное хранение с использованием трехуровневой схемы (GFS)](backup-azure-backup-cloud-as-tape.md). То есть можно определить политику объединенного хранения, включающую политики ежедневного, еженедельного, ежемесячного и ежегодного хранения. В этом примере мы создаем массив, представляющий сложную схему хранения, которая нам необходима, а затем настраиваем диапазон хранения с помощью командлета [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762).

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### Настройка расписания резервного копирования
При указании цели защиты с помощью командлета ```Set-DPMPolicyObjective``` DPM автоматически задаст расписание резервного копирования по умолчанию. Для изменения расписания по умолчанию используйте командлет [Get DPMPolicySchedule](https://technet.microsoft.com/library/hh881749), а затем командлет [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723).

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

В приведенном выше примере ```$onlineSch``` является массивом с четырьмя элементами, содержащий существующее расписание оперативной защиты для группы защиты в схеме GFS:

1. ```$onlineSch[0]``` будет содержать ежедневное расписание
2. ```$onlineSch[1]``` будет содержать еженедельное расписание
3. ```$onlineSch[2]``` будет содержать ежемесячное расписание
4. ```$onlineSch[3]``` будет содержать ежегодное расписание

Поэтому если необходимо изменить еженедельное расписание, необходимо ссылаться на ```$onlineSch[1]```.

### Начальное резервное копирование
Если вы создаете резервные копии источника данных впервые, DPM создаст исходную реплику, которая создаст в томе реплики DPM копию защищаемого источника данных. Это действие можно запланировать на определенное время или выполнить вручную с помощью командлета [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) с параметром ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### Фиксация изменений в группу защиты
Наконец, необходимо зафиксировать изменения до того, как DPM выполнит резервное копирование в соответствии с новой конфигурацией группы защиты. Для этого воспользуйтесь командлетом [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758).

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Просмотр точек резервного копирования
Чтобы получить список всех точек восстановления для источника данных, можно использовать командлет [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746). В данном примере мы: выгрузим данные из всех групп защиты на сервер DPM, которые будут храниться в массиве ```$PG```; получим источники данных, соответствующие ```$PG[0]```; получим все точки восстановления для источника данных.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Восстановление данных, защищенных в Azure
Восстановление данных представляет собой сочетание объектов ```RecoverableItem``` и ```RecoveryOption```. В предыдущем разделе мы получили список точек резервного копирования для источника данных.

В примере ниже демонстрируется восстановление виртуальной машины Hyper-V из службы архивации Azure путем объединения резервных точек с целевым объектом для восстановления. А именно:

- Создадим параметр восстановления, используя командлет [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592).
- Выполним выборку массива точек резервного копирования с помощью командлета ```Get-DPMRecoveryPoint```.
- Выберем точку резервного копирования для восстановления.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Команды можно с легкостью расширить для любого типа источника данных.

## Дальнейшие действия
Дополнительные сведения о службе архивации Azure для DPM см. в разделе [Общие сведения о службе архивации DPM Azure](backup-azure-dpm-introduction.md)

<!----HONumber=July15_HO4-->