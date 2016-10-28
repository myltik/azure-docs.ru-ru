<properties
	pageTitle="Репликация виртуальных машин VMware в Azure с помощью Site Recovery и Azure Automation DSC | Microsoft Azure"
	description="В этой статье описывается, как использовать Azure Automation DSC для автоматизированного развертывания службы Mobility Service Azure Site Recovery и агента Azure для виртуальных машин и физических компьютеров в Azure."
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Репликация виртуальных машин VMware в Azure с помощью Site Recovery и Azure Automation DSC

В Operations Management Suite предоставляется комплексное решение для архивации и аварийного восстановления, которое можно использовать в плане обеспечения непрерывности бизнес-процессов.

Мы начали этот путь с Hyper-V, используя реплику Hyper-V. Но мы расширили возможности для поддержки разнородных конфигураций, так как клиенты используют несколько гипервизоров и платформ в своих облаках.

Если вы используете рабочие нагрузки VMware и (или) физические серверы, то сервер управления выполняет все компоненты Azure Site Recovery, запущенные в вашей среде, чтобы обрабатывать обмен данными и репликацию данных в Azure, если Azure используется в качестве назначения.

## Развертывание службы Mobility Service Site Recovery с помощью Automation DSC
Начнем с краткого разбора функций этого сервера управления.

Сервер управления выполняет несколько ролей. Одна из них, *роль конфигурации*, координирует обмен данными и управляет процессами репликации и восстановления данных.

Кроме того, *роль обработки* функционирует как шлюз репликации. Эта роль получает данные репликации с защищенных исходных компьютеров, оптимизирует их за счет кэширования, сжатия и шифрования и отправляет в учетную запись хранения Azure. Одной из функций роли обработки также является обеспечение принудительной установки службы Mobility Service на защищенные компьютеры и выполнение автоматического обнаружения виртуальных машин VMware.

В случае восстановления размещения из Azure роль *главной цели* будет отвечать за обработку данных репликации в рамках этой операции.

В случае защищенных компьютеров мы полагаемся на *службу Mobility Service*. Этот компонент развертывается на каждом компьютере (виртуальной машине VMware или физическом сервере), для которого требуется выполнить репликацию в Azure. Он записывает операции записи данных на компьютер и направляет их на сервер управления (роль обработки).

При обеспечении непрерывности бизнес-процессов важно понимать характер рабочих нагрузок, инфраструктуры и задействованных компонентов. Это позволит обеспечить требуемые значения целевого времени восстановления (RTO) и целевой точки восстановления (RPO). В этом контексте служба Mobility Service критически важна для обеспечения надлежащей защиты рабочих нагрузок.

Как оптимальным образом можно обеспечить надежную защищенную конфигурацию, используя компоненты Operations Management Suite?

В этой статье приведен пример того, как можно использовать настройку требуемого состояния службы автоматизации Azure (DSC) с Site Recovery, чтобы обеспечить следующее:

- Служба Mobility Service и агент виртуальной машины Azure развернуты на компьютерах Windows, которые вы хотите защитить.
- Служба Mobility Service и агент виртуальной машины Azure всегда выполняются, если целью репликации является Azure.

## Предварительные требования

- Репозиторий для хранения пакета установки.
- Репозиторий для хранения парольной фразы, которую требуется зарегистрировать на сервере управления.

 > [AZURE.NOTE] Для каждого сервера управления создается уникальная парольная фраза. Если планируется развернуть несколько серверов управления, то необходимо удостовериться, что в файле passphrase.txt хранится правильная парольная фраза.

- Компонент Windows Management Framework 5.0 (WMF) должен быть установлен на компьютерах, для которых необходимо включить защиту (требование для Automation DSC).

 > [AZURE.NOTE] Если вы хотите использовать DSC для компьютеров Windows, на которых установлен компонент WMF 4.0, то ознакомьтесь с подразделом [Использование DSC в отключенных средах] \(#Использование DSC в отключенных средах).

Службу Mobility Service можно установить с помощью командной строки. Соответствующая команда принимает несколько аргументов. Поэтому нам нужны двоичные файлы (после извлечения из пакета установки). Эти файлы необходимо сохранить в расположение, из которого их можно извлечь с помощью конфигурации DSC.

## Шаг 1. Извлечение двоичных файлов

1. Чтобы извлечь файлы, необходимые для этой установки, перейдите в следующий каталог на сервере управления.

	**\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**

    В этой папке должен быть MSI-файл с таким именем.

    **Microsoft-ASR\_UA\_version\_Windows\_GA\_date\_Release.exe**

    Извлеките установщик, используя следующую команду.

    **.\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract**

2. Выберите все файлы и отправьте их в сжатую папку (в формате ZIP).

Теперь у вас есть двоичные файлы, необходимые для автоматизации установки службы Mobility Service с помощью Automation DSC.

### Парольная фраза

Далее необходимо определить расположение для размещения этой сжатой папки. Для хранения парольной фразы, которая требуется для установки, можно использовать учетную запись хранения Azure, как показано далее. Затем в ходе процесса агент будет зарегистрирован на сервере управления.

Парольную фразу, полученную при развертывании сервера управления, можно сохранить в TXT-файле passphrase.txt.

Поместите сжатую папку и парольную фразу в выделенный контейнер в учетной записи хранения Azure.

![Расположение папки](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

При необходимости эти файлы можно сохранить в общем ресурсе в сети. Только следует убедиться, что у ресурса DSC, который будет использоваться позже, имеется доступ к пакету установки и парольной фразе.

## Шаг 2. Создание конфигурации DSC

Установка зависит от WMF 5.0. Чтобы компьютер успешно применил конфигурацию посредством Automation DSC, необходим компонент WMF 5.0.

В среде используется следующий пример конфигурации DSC.

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
В рамках этой конфигурации выполняется следующее:

- Переменные сообщают конфигурации, где можно получить двоичные файлы для службы Mobility Service, агента виртуальной машины Azure и парольную фразу, а также где хранить выходные данные.
- С помощью конфигурации будет импортирован ресурс DSC xPSDesiredStateConfiguration, чтобы можно было использовать `xRemoteFile` для скачивания файлов из репозитория.
- Затем будет создан каталог для хранения двоичных файлов.
- Ресурс архива извлечет файлы из сжатой папки.
- Ресурс Install пакета установит службу Mobility Service с помощью установщика UNIFIEDAGENT. EXE с использованием определенных аргументов. (Переменные, составляющие эти аргументы, должны быть изменены в соответствии с вашей средой.)
- Ресурс AzureAgent пакета установит агент виртуальной машины Azure. Это рекомендуется сделать для каждой виртуальной машины, работающей в Azure. Агент виртуальной машины Azure также делает возможным добавление расширений виртуальной машины после ее отработки отказа.
- Ресурс или ресурсы службы гарантируют, что связанные службы Mobility Service и службы Azure всегда будут запущены.

Сохраните конфигурацию как **ASRMobilityService**.

>[AZURE.NOTE] \(Не забудьте заменить CSIP в конфигурации в соответствии с реальным сервером управления, чтобы обеспечить правильное подключение агента. При этом требуется использовать правильную парольную фразу.)

## Шаг 3. Передача в Automation DSC

Так как созданная конфигурация DSC импортирует требуемый модуль ресурса DSC (xPSDesiredStateConfiguration), необходимо импортировать этот модуль в службу автоматизации, прежде чем передать конфигурацию DSC.

Войдите в учетную запись службы автоматизации и щелкните **Ресурсы-контейнеры** > **Модули** > **Просмотреть коллекцию**.

Здесь можно найти модуль и импортировать его в вашу учетную запись.

![Импорт модуля](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

После этого перейдите на компьютер, где установлены модули Azure Resource Manager, и импортируйте созданную конфигурацию DSC.

### Импорт командлетов

Используя PowerShell, войдите в свою подписку Azure. Измените командлеты в соответствии со своей средой. Запишите данные своей учетной записи службы автоматизации в переменную.

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Передайте конфигурацию в Automation DSC, используя следующий командлет.

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### Компиляция конфигурации в Automation DSC

Теперь нужно скомпилировать конфигурацию в Automation DSC, чтобы можно было начать регистрацию узлов в ней. Для этого нужно выполнить следующий командлет.

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Это может занять несколько минут, так как конфигурация фактически развертывается в размещенной службе извлечения DSC.

После завершения компиляции конфигурации можно получить сведения о задании, используя PowerShell (Get-AzureRmAutomationDscCompilationJob) или [портал Azure](https://portal.azure.com/).

![Получение задания](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Конфигурация DSC успешно опубликована и передана в Automation DSC.

## Шаг 4. Подключение компьютеров к Automation DSC
>[AZURE.NOTE] Для выполнения этого сценария требуется, чтобы на компьютерах Windows была установлена последняя версия WMF. Вы можете скачать и установить правильную версию для своей платформы, перейдя в [Центр загрузки](https://www.microsoft.com/download/details.aspx?id=50395).

Теперь вы создадите метаконфигурацию для DSC, которая будет применена к узлам. Чтобы сделать это, необходимо получить URL-адрес конечной точки и первичный ключ для выбранной учетной записи службы автоматизации в Azure. Эти значения можно найти в колонке **Все параметры** (раздел **Ключи**) учетной записи службы автоматизации.

![Значения ключей](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

В этом примере имеется физический сервер Windows Server 2012 R2, который необходимо защитить с помощью Site Recovery.

### Проверка всех ожидающих операций переименования файлов в реестре

Прежде чем связать сервер с конечной точкой Automation DSC, рекомендуется проверить все отложенные операции переименования файлов в реестре. Необходимая для них отложенная перезагрузка может препятствовать выполнению установки.

Чтобы убедиться в отсутствии ожидающей перезагрузки на сервере, выполните следующий командлет:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Если отображается пустое окно, можно продолжить процедуру. В противном случае эту проблему нужно решить, перезагрузив сервер в период обслуживания.

Чтобы применить конфигурацию к серверу, запустите интегрированную среду сценариев PowerShell и выполните приведенный ниже сценарий. По сути это локальная конфигурация DSC, которая сообщит механизму локального диспетчера конфигурации о необходимости регистрации в службе Automation DSC и получения конкретной конфигурации (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Эта конфигурация приведет к регистрации механизма локального диспетчера конфигурации в Automation DSC. Она также определяет работу этого механизма и то, как ему действовать при изменении конфигурации (ApplyAndAutoCorrect) и как продолжить настройку, если требуется перезагрузка.

После выполнения данного сценария должна начаться регистрация узла в Automation DSC.

![Выполняется регистрация узла](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Теперь зарегистрированный узел отображается на портале Azure.

![Зарегистрированный узел на портале](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Чтобы убедиться, что узел зарегистрирован надлежащим образом, выполните на сервере следующий командлет PowerShell.

```powershell
Get-DscLocalConfigurationManager
```

Чтобы проверить состояние после извлечения и применения конфигурации к серверу, выполните следующий командлет.

```powershell
Get-DscConfigurationStatus
```

Результат показывает, что сервер успешно извлек конфигурацию.

![Выходные данные](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Кроме того, в пакете установки службы Mobility Service есть собственный журнал, расположенный в папке *системный\_диск*\\ProgramData\\ASRSetupLogs.

Вот и все! Вы успешно развернули и зарегистрировали службу Mobility Service на компьютере, который необходимо защитить с помощью Site Recovery. Благодаря DSC необходимые службы всегда будут запущены.

![Развертывание завершено успешно](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

После того, как сервер управления определит, что развертывание успешно выполнено, можно будет настроить защиту и включить репликацию на компьютере с помощью Site Recovery.

## Использование DSC в отключенных средах

Даже если компьютеры не подключены к Интернету, DSC по-прежнему поможет развернуть и настроить службу Mobility Service для рабочих нагрузок, которые необходимо защитить.

Можно создать собственный экземпляр сервера извлечения DSC в своей среде, по сути, предоставляя те же возможности, что и Automation DSC. То есть клиенты будут извлекать конфигурацию (после регистрации) в конечную точку DSC. Тем не менее, возможен и другой вариант — можно вручную отправить конфигурацию DSC на компьютеры, локально или удаленно.

Обратите внимание, что в этом примере добавлен параметр для имени компьютера. Удаленные файлы теперь находятся в удаленном общем ресурсе, который должен быть доступен компьютерам, которые нужно защитить. В конце сценария задействуется конфигурация DSC, а затем начинается ее применение к целевому компьютеру.

### Предварительные требования

Убедитесь, что модуль xPSDesiredStateConfiguration PowerShell установлен. На компьютерах Windows с установленным компонентом WMF 5.0 можно установить модуль xPSDesiredStateConfiguration, просто выполнив на них следующий командлет.

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Можно также скачать и сохранить этот модуль на случай, если понадобится распространить его на компьютеры Windows, на которых установлен компонент WMF 4.0. Выполните этот командлет на компьютере, на котором установлен компонент PowerShellGet (WMF 5.0).

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Кроме того, убедитесь, что на компьютерах с WMF 4.0 также установлено [обновление KB2883200 для Windows 8.1](https://www.microsoft.com/download/details.aspx?id=40749).

Приведенную ниже конфигурацию можно применить к компьютерам Windows с WMF 5.0 и WMF 4.0.

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Если необходимо создать экземпляр собственного сервера извлечения DSC в корпоративной сети для имитации тех же возможностей, что предоставляет Automation DSC, ознакомьтесь с разделом [Настройка опрашивающего веб-сервера DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## Развертывание конфигурации DSC с помощью шаблона Azure Resource Manager (необязательно)

Вы этой статье были рассмотрены способы создания собственной конфигурации DSC для автоматизированного развертывания службы Mobility Service и агента виртуальной машины Azure, а также было уделено внимание тому, как обеспечить их работу на компьютерах, которые необходимо защитить. У нас также есть шаблон Azure Resource Manager, с помощью которого можно развернуть эту конфигурацию DSC в новой или существующей учетной записи службы автоматизации Azure. Этот шаблон будет использовать входные параметры для создания ресурсов службы автоматизации, содержащих переменные среды.

После развертывания шаблона можно просто обратиться к шагу 4 этого руководства, чтобы подключить свои компьютеры.

Шаблон выполнит следующее:

1. Использует имеющуюся или создаст учетную запись службы автоматизации.
2. Примет входные параметры для:
	- ASRRemoteFile — расположения, в котором сохранен пакет установки службы Mobility Service;
	- ASRPassphrase — расположения, в котором сохранен файл passphrase.txt;
	- ASRCSEndpoint — IP-адреса сервера управления.
3. Импортирует модуль xPSDesiredStateConfiguration PowerShell.
4. Создаст и скомпилирует конфигурацию DSC.

Приведенные выше действия будут выполнены в правильном порядке, чтобы вы могли приступить к подключению компьютеров для их защиты.

Шаблон с инструкциями по развертыванию находится на сайте [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Разверните шаблон с помощью PowerShell, как показано ниже.

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## Дальнейшие действия

После развертывания агентов Mobility Service можно [включить репликацию](site-recovery-vmware-to-azure.md#step-6-replicate-applications) для виртуальных машин.

<!---HONumber=AcomDC_0831_2016-->
