<properties
	pageTitle="Служба архивации Azure — развертывание и управление резервным копированием для DPM с помощью Windows PowerShell | Microsoft Azure"
	description="Узнайте, как осуществлять развертывание и управление службой архивации Azure для серверов Data Protection Manager (DPM) с помощью Windows PowerShell"
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
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Развертывание и управление резервным копированием в Azure для серверов Data Protection Manager (DPM) с помощью Windows PowerShell
В этой статье описано, как использовать интерфейс командной строки Windows PowerShell® для настройки службы архивации Azure на сервере DPM и для управления резервным копированием и восстановлением данных.

## Настройка среды Windows PowerShell
Для использования Windows PowerShell в целях управления службой архивации Azure необходимо иметь соответствующую версию Windows PowerShell и задать соответствующие настройки переменных среды.

Убедитесь, что Windows PowerShell имеет версию 3.0 или 4.0. Чтобы узнать версию Windows PowerShell, введите в командной строке Windows/DPM PowerShell эту команду.

```
$PSVersionTable
```

Появятся такие сведения:

| Имя | Значение |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

Убедитесь, что в строке **PSVersion** указано значение 3.0 или 4.0. Если указано другое значение, см. статью [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

## Настройка и регистрация
### Установка агента службы архивации Azure на сервер DPM
Для установки агента службы архивации Azure необходимо загрузить установщик и разместить его в системе Windows Server. Последнюю версию установщика можно загрузить в [Центре загрузки Майкрософт](aka.ms/azurebackup_agent). Сохраните установщик в удобном для вас месте, например *C:*.

Чтобы установить агент, в консоли Windows PowerShell с повышенными привилегиями выполните следующую команду:

```
PS C:> MARSAgentInstaller.exe /q
```

Агент будет установлен с параметрами по умолчанию. Установка займет всего несколько минут и пройдет в фоновом режиме. Если параметр */nu* не будет указан, в конце установки откроется окно «Обновления Windows» для проверки наличия обновлений.

Агент появится в списке установленных программ. Чтобы просмотреть список установленных программ, перейдите в **Панель управления** > **Программы** > **Программы и компоненты**.

![Агент установлен](./media/backup-dpm-automation/installed-agent-listing.png)

#### Параметры установки
Чтобы просмотреть все доступные в командной строке параметры, используйте следующую команду:

```
PS C:> MARSAgentInstaller.exe /?
```

Доступны следующие параметры.

| Параметр | Сведения | значение по умолчанию |
| ---- | ----- | ----- |
| /q | Тихая установка | - | | / p:"местоположение" | Путь к папке установки агента службы архивации Azure. | C:\\Program Files\\Microsoft Azure Recovery Services Agent | | /s:"местоположение" | Путь к папке кэша агента службы архивации Azure. | C:\\Program Files\\Microsoft Azure Recovery Services Agent\\Scratch | | /m | Согласиться на получение обновлений от Майкрософт | - | | /nu | Не проверять наличие обновлений после завершения установки | - | | /d | Удаляет агент служб восстановления Microsoft Azure | - | | /ph | Адрес узла прокси-сервера | - | | /po | Номер порта узла прокси-сервера | - | | / pu | Имя пользователя узла прокси-сервера | - | | /pw | Пароль прокси-сервера | - |

### Регистрация в службе архивации Azure
Перед регистрацией в службе архивации Azure убедитесь, что указанные далее условия выполняются: у вас есть действительная подписка Azure; вы создали хранилище службы архивации; вы загрузили учетные данные хранилища и сохранили их в удобном для вас месте (например, *C:\\Downloads*). Для удобства вы можете изменить учетные данные хранилища.

Регистрация компьютера в хранилище выполняется с помощью командлета [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787):

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

Как видно, сервер с именем «TestingServer» регистрируется в хранилище Microsoft Azure на основании учетных данных хранилища, которые находятся в папке *C: *.

> [AZURE.IMPORTANT]Не используйте относительные пути для указания файла с учетными данными хранилища. Укажите абсолютный путь в качестве входных данных командлета.

### Настройка параметров резервного копирования DPM (параметры подписки) для оперативного резервного копирования
После регистрации сервера DPM для оперативной защиты его параметры подписки установлены по умолчанию и задают различные функции резервного копирования. Если вы хотите изменить параметры подписки, сначала вам необходимо получить существующие параметры, а затем изменить их в соответствии с требованиями.

#### Получение существующих параметров подписки для сервера DPM
Чтобы настроить параметры подписки DPM Online, сначала получите существующие параметры с помощью командлета [Get DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx):

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Выполните эту команду, чтобы получить объект параметров подписки, который может использоваться для настройки различных параметров и их дальнейшего сохранения. Выполнение этой команды для нового сервера DPM вернет параметры по умолчанию.

#### Изменение параметров подписки
С помощью командлета [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) можно настроить следующие параметры оперативного резервного копирования данных подписки DPM:

##### Промежуточная область
Для агента службы архивации Azure, работающего на сервере DPM, необходимо предоставить временное хранилище для восстановленных из облака данных (локальная промежуточная область). Чтобы настроить промежуточную область, вы можете воспользоваться показанной ниже командой. В данном случае промежуточная область задана в папке «C:\\StagingArea» для объекта **$setting**, но эта конфигурация не будет применена на сервере DPM, пока параметры не будут зафиксированы.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]Убедитесь, что папка, указанная в предыдущей команде, уже существует. В противном случае при сохранении параметров подписки появится сообщение об ошибке

##### Сеть
Подключение компьютера с DPM к Интернету осуществляется через прокси-сервер. Параметры прокси-сервера можно применить и для агента. В нашем случае прокси-сервер не используется, поэтому мы явным образом удаляем все данные прокси-сервера.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

Управлять использованием пропускной способности для выбранных дней недели можно с помощью параметров *пропускной способности в рабочее время* и *пропускной способности в нерабочее время*. В данном случае мы не будем настраивать регулирование

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### Параметры шифрования
Для защиты конфиденциальности данных резервные копии данных, отправляемые в службу архивации Azure, зашифровываются. Используемая для шифрования парольная фраза является «паролем» для расшифровки данных во время их восстановления.

> [AZURE.IMPORTANT]После создания фразы надежно сохраните ее и никому не сообщайте о ней.

В следующем примере первая команда преобразует строку «passphrase123456789» в защищенную строку и присваивает переменной с именем «$Passphrase» защищенную строку. Вторая команда задает защищенную строку в «$Passphrase» в качестве пароля для шифрования резервных копий

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### Сохранение параметров подписки
Зафиксируйте изменения на сервере DPM с помощью функции *— Зафиксировать*

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]Применение параметров происходит только после их фиксации.

## Защита данных в службе архивации Azure
Далее описано, как добавить на DPM рабочий сервер и включить защиту данных на диске и сети в Azure. С помощью примеров мы покажем, как создавать резервные копии файлов и папок. Таким же способом можно создавать резервные копии любых поддерживаемых источников данных. Для начала вам необходимо установить и зарегистрировать сервер DPM для оперативной защиты.

Все резервные копии DPM находятся под управлением группы защиты (ГЗ), которая состоит из четырех частей.

1. **Члены группы защиты** — это список всех защищаемых объектов, которые вы хотите защитить в рамках одной ГЗ. Например, вы можете защитить рабочие виртуальные машины в одной ГЗ, а тестовые виртуальные машины — в другой ГЗ, так как у них могут быть разные требования для резервного копирования.
2. **Способ защиты данных** — используется для указания места, в котором необходимо защитить данные. В качестве примера мы обеспечим защиту данных на диске и оперативную защиту в облаке Azure.
3. **Расписание резервного копирования** — указывает, когда необходимо выполнять резервное копирование и как часто данные должны быть синхронизированы между копией на сервере DPM и рабочим сервером.
4. **Расписание хранения** — определяет период хранения точек восстановления в Azure.

### Создание группы защиты и резервное копирование данных в соответствии с расписанием
#### Добавление рабочего сервера на сервер DPM
Перед созданием резервных копий данных на рабочем сервере убедитесь, что агент DPM установлен на рабочем сервере и управляется с помощью DPM. Для установки агента DPM и настройки его резервного копирования с помощью соответствующего сервера DPM выполните [такие](https://technet.microsoft.com/library/bb870935.aspx) действия.

#### Создание ГЗ
Так как мы будем использовать автоматическое резервное копирование данных, в этой статье предположим, что заданных настроек нет. Мы начнем с создания новой ГЗ с помощью командлета [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx) .

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Перед внесением изменений в ГЗ переключите ее в режим изменений с помощью командлета [Get DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx).

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

Указанные командлеты создают ГЗ с именем «ProtectGroup01», но на данном этапе они не выполнят резервное копирование. Вам необходимо указать, резервные копии каких объектов нужно создать, когда будет выполняться резервное копирование и где будут храниться резервные копии.

### Добавление членов группы в ГЗ
Чтобы добавить источник данных в ГЗ, нужно выполнить несколько действий

#### Получение рабочего сервера (РС), файлы которого подлежат резервному копированию
Вы можете извлечь все данные из РС, на котором установлен агент DPM, управляемый с сервера DPM, с помощью командлета [Get DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx). В этом примере мы отфильтруем и настроим для резервного копирования только РС с именем «productionserver01».

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### Запуск запроса для получения списка источников данных на РС
Вы можете запустить запрос на компьютере с РС, чтобы получить список всех источников данных, резервные копии которых может сделать DPM, с помощью командлета [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx) . В данном случае вы можете отфильтровать источник данных «D:\\» на РС, для которого нужно настроить защиту.

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### Добавление источника данных для защиты
Вы можете добавить источник данных в ГЗ с именем «ProtectGroup01», которую мы создали ранее с помощью командлета [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx)

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### Выбор способа защиты данных
После добавления источника данных в ГЗ укажите способ защиты. В данном случае мы настроим ГЗ для кратковременной защиты на диске и длительной защиты на магнитной ленте.

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Задание целей точек восстановления
#### Настройка диапазона хранения
Вы можете задать диапазон хранения РС, используя командлет [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx). Приведенная далее команда задает *диапазон хранения* и *частоту синхронизации* точек восстановления для диска.

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

Для точек оперативного восстановления вы можете настроить диапазоны хранения для различных схем резервного копирования GFS («дед-отец-сын») (т. е. ежедневно, еженедельно, ежемесячно и ежегодно). В данном примере мы создадим объект с разными диапазонами хранения, а затем настроим диапазон оперативного хранения с помощью объекта, как показано в следующем примере.

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### Настройка расписания резервного копирования
При указании цели защиты с помощью командлета **Set-PolicyObjective** DPM автоматически задаст расписание по умолчанию. Для изменения расписания по умолчанию используйте командлет [Get DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx), а затем командлет [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx).

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### Создание реплики
Если вы создаете резервные копии источника данных впервые, DPM создаст исходную реплику, которая создаст в томе реплики DPM копию защищаемого источника данных. Также вы можете запланировать автоматическое создание реплики в определенное время или создать ее вручную с помощью командлета [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx).

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### Фиксация ГЗ
Наконец, до того как DPM начнет резервное копирование в соответствии с настройками ГЗ с помощью командлета [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx), вам необходимо зафиксировать изменения.

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Получение списка всех точек оперативного восстановления
Чтобы получить список всех точек восстановления для источника данных, можно использовать командлет [Get DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx). В данном примере мы: выгрузим данные со всех ГЗ на сервер DPM, которые будут храниться в массиве «$PG»; получим источники данных, соответствующие «$PG[0]»; получим все точки восстановления для источника данных.

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Восстановление данных, защищенных в Azure
В этом примере мы покажем, как восстановить виртуальную машину Hyper-V из точки оперативного восстановления, однако вы можете использовать эти команды для любого типа источника данных.

Сначала создайте параметр восстановления, используя командлет [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx). В рамках показанного далее примера мы будем восстанавливать источник данных Hyper-V в альтернативное расположение.

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

После настройки параметра восстановления ваша точка оперативного восстановления, которую мы извлекли ранее в разделе [Получение списка всех точек оперативного восстановления](#Getting-a-list-of-all-Online-Recovery-Points), будет восстановлена.

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## Дальнейшие действия
Дополнительные сведения о службе архивации Azure для DPM см. в разделе [Общие сведения о службе архивации DPM Azure](backup-azure-dpm-introduction.md)

<!---HONumber=62-->