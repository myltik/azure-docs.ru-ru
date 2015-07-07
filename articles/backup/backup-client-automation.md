<properties
	pageTitle="Развертывание и управление резервным копированием для Windows Server и Client с помощью Windows PowerShell | Microsoft Azure"
	description="Узнайте, как осуществлять развертывание и управление службой архивации Azure с помощью Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Развертывание и управление резервным копированием в Azure для Windows Server и Windows Client с помощью Windows PowerShell
В этой статье показано, как использовать интерфейс командной строки Windows PowerShell® для настройки службы архивации Azure в Windows Server или Windows Client и для управления резервным копированием и восстановлением данных.

## Настройка среды Windows PowerShell
Для использования Windows PowerShell в целях управления службой архивации Azure необходимо иметь соответствующую версию Windows PowerShell и задать соответствующие настройки переменных среды.

## Настройка и регистрация
С помощью Windows PowerShell можно автоматизировать следующие задачи для установки и регистрации: «Установка агента службы архивации Azure», «Регистрация в службе архивации Azure» и «Сеть»

### Установка агента службы архивации Azure.
Для установки агента службы архивации Azure необходимо загрузить установщик и разместить его в системе Windows Server. Последнюю версию установщика можно загрузить в [Центре загрузки Майкрософт](aka.ms/azurebackup_agent). Сохраните установщик в удобном для вас месте, например *C:\\Downloads*. Чтобы установить агент, в консоли Windows PowerShell с повышенными привилегиями выполните следующую команду:

```
PS C:> MARSAgentInstaller.exe /q
```

Агент будет установлен с параметрами по умолчанию. Установка займет всего несколько минут и пройдет в фоновом режиме. Если параметр **/nu** не будет указан, в конце установки откроется окно **Обновления Windows** для проверки наличия обновлений.

Агент появится в списке установленных программ. Чтобы просмотреть список установленных программ, перейдите в **Панель управления** > **Программы** > **Программы и компоненты**.

![Агент установлен](./media/backup-client-automation/installed-agent-listing.png)

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
Перед регистрацией в службе архивации Azure убедитесь, что указанные далее [условия](backup-try-azure-backup-in-10-mins.md) выполняются: у вас есть действительная подписка Azure; вы создали хранилище службы архивации; вы загрузили учетные данные хранилища и сохранили их в удобном для вас месте (например, *C:\\Downloads*). Для удобства вы можете изменить учетные данные хранилища.

Регистрация компьютера в хранилище выполняется с помощью командлета [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx):

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]Не используйте относительные пути для указания файла с учетными данными хранилища. Укажите абсолютный путь в качестве входных данных командлета.


### Сеть
Если подключение компьютера под управлением Windows к Интернету осуществляется через прокси-сервер, параметры этого прокси-сервера могут сообщаться агенту. В нашем случае прокси-сервер не используется, поэтому мы явным образом удаляем все данные прокси-сервера.

Управлять использованием пропускной способности для выбранных дней недели можно с помощью параметров *пропускной способности в рабочее время* и *пропускной способности в нерабочее время*.

Внесение сведений о прокси-сервере и пропускной способности выполняется с помощью командлета [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx):

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Параметры шифрования
Для защиты конфиденциальности данных резервные копии данных, отправляемые в службу архивации Azure, зашифровываются. Используемая для шифрования парольная фраза является «паролем» для расшифровки данных во время их восстановления. После создания фразы надежно сохраните ее и никому не сообщайте о ней.

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Удаление агента службы архивации Azure.
Удалить агент службы архивации Azure можно с помощью следующей команды:

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

При удалении с компьютера двоичных файлов агента происходит следующее: с компьютера удаляется фильтр файлов и прекращается отслеживание изменений; сведения о политике удаляются с компьютера, но сохраняются в службе; удаляется расписание резервного копирования и прекращается создание резервных копий.

Однако данные, хранящиеся в Azure, останутся там в течение установленного вами периода политики хранения. Предыдущие точки восстановления автоматически рассматриваются как устаревшие.

## Удаленное управление
Windows PowerShell обеспечивает удаленное управление агентом службы архивации Azure, политикой и источниками данных. Компьютер, который будет управляться удаленно, необходимо специально подготовить.

По умолчанию служба WinRM настроена на запуск вручную. Установите тип запуска *Automatic*. Служба запустится. Чтобы проверить работу службы WinRM, присвойте значению свойства Status параметр *Running*.

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

Теперь компьютером можно управлять удаленно. Начните с установки агента. Например, следующий сценарий копирует агент на удаленный компьютер и устанавливает его.

```
PS C:> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## Дальнейшие действия
Дополнительные сведения о службе архивации Azure для Windows Server и Client см. в разделе [Общие сведения о службе архивации Azure](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->