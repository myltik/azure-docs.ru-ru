.<properties
	pageTitle="Репликация виртуальных машин VMware в Azure с помощью Site Recovery и Azure Automation DSC | Microsoft Azure"
	description="В этой статье описывается использование Azure Automation DSC для автоматического развертывания службы Mobility Service ASR и агента Azure для виртуальных машин и физических компьютеров в Azure."
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

.<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Репликация виртуальных машин VMware в Azure с помощью Site Recovery и Azure Automation DSC

В OMS предоставляется комплексное решение для резервного копирования и аварийного восстановления, которое можно использовать как часть плана обеспечения непрерывности бизнес-процессов.

Изначально в этом решении использовалось ПО Hyper-V и реплика Hyper-V, но затем мы обеспечили поддержку других вариантов установки, так как мы знаем, что в облаках наших клиентов используется несколько низкоуровневых оболочек и платформ.

Если вы используете рабочие нагрузки VMware и/или физические серверы, сервер управления, на котором выполняются все компоненты Site Recovery, запущенные в вашей среде, будет обрабатывать обмен данными и репликацию данных в Azure, если Azure используется в качестве места назначения.

## Развертывание службы Mobility Service ASR с использованием OMS Automation DSC
Начнем с краткого разбора функций этого сервера управления.

На сервере управления выполняется несколько ролей, например роль **конфигурации**, которая координирует обмен данными и управляет процессами репликации и восстановления данных.

Роль **процесса** выступает в качестве шлюза репликации, который получает данные репликации с защищенных исходных компьютеров и оптимизирует их путем кэширования, сжатия и шифрования, прежде чем отправить в учетную запись хранения Azure. Одной из функций этой роли также является обеспечение принудительной установки службы Mobility Service на защищенные компьютеры и выполнение автоматического обнаружения виртуальных машин VMware.

В случае восстановления размещения из Azure **главная целевая** роль будет отвечать за обработку данных репликации в рамках этой операции.

Что касается защищенных компьютеров, мы полагаемся на службу **Mobility Service** — компонент, развертываемый на каждом компьютере (виртуальной машине VMware или физическом сервере), который требуется реплицировать в Azure. Она захватывает операции записи данных на компьютер и направляет их на сервер управления (роль процесса).

При обеспечении непрерывности бизнес-процессов важно понимать природу рабочих нагрузок, инфраструктуры и задействованных компонентов, чтобы достичь необходимых показателей RTO и RPO. В этом контексте служба Mobility Service критически важна для обеспечения надлежащей защиты рабочих нагрузок.

Каким оптимальным образом с использованием компонентов OMS можно обеспечить надежную защищенную установку?

В этой статье приведен пример использования OMS Automation DSC с OMS Site Recovery, позволяющий развернуть службу Mobility Service и агент виртуальной машины Azure на компьютерах Windows, которые необходимо защитить, а также обеспечить их постоянную работу, если Azure выступает в качестве целевого объекта репликации.

## Предварительные требования

- Репозиторий для хранения пакета установки.
- Репозиторий для хранения парольной фразы, которую требуется зарегистрировать на сервере управления.
- Компонент Windows Management Framework 5.0, установленный на компьютерах, для которых необходимо включить защиту (требование для OMS Automation DSC).

Если необходимо использовать DSC на компьютерах Windows с WMF 4.0, см. раздел "Использование DSC в отключенных средах".

(Для каждого сервера управления создается уникальная парольная фраза. Если планируется развернуть несколько серверов управления, необходимо удостовериться, что в файле passphrase.txt хранится правильная парольная фраза.)

Службу Mobility Service можно установить с помощью командной строки. Она принимает несколько аргументов.

Поэтому нам нужны двоичные файлы (после извлечения их из нашего пакета установки). Эти файлы необходимо сохранить в расположение, из которого их можно извлечь с помощью конфигурации DSC.

## Шаг 1. Извлечение двоичных файлов

1. Чтобы извлечь файлы, необходимые для установки, перейдите в следующий каталог на сервере управления: **\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**.

    В этой папке будет содержаться MSI-файл с таким именем:

    **Microsoft-ASR\_UA\_<версия>_Windows\_GA_<дата>\_Release.exe**.

    Извлеките установщик, используя следующую команду.

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. Выберите все файлы и отправьте их в сжатую папку ZIP.

Это все! Теперь у вас есть двоичные файлы, необходимые для автоматизации установки службы Mobility Service с помощью OMS Automation DSC.

### Парольная фраза
Далее необходимо определить расположение для размещения этой сжатой папки. Можно использовать учетную запись хранения Azure, как показано ниже, чтобы сохранить парольную фразу, необходимую для установки. Тогда агент будет регистрироваться на сервере управления в этом процессе.

Парольную фразу, полученную при развертывании сервера управления, можно сохранить в TXT-файле passphrase.txt.

Поместите сжатую папку и парольную фразу в выделенный контейнер в учетной записи хранения Azure.

![Расположение папки](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

При необходимости эти файлы можно сохранить в общей папке в сети. Ресурс DSC, который будет использоваться позже, должен иметь доступ к пакету установки и парольной фразе.

## Шаг 2. Создание конфигурации DSC
Для установки требуется WMF 5.0. Это значит, что чтобы компьютер применил конфигурацию посредством OMS Automation DSC, понадобится наличие WMF 5.0.

В рассматриваемой среде используется следующий пример конфигурации DSC.

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

- Переменные сообщают конфигурации, где можно получить двоичные файлы для службы Mobility Service и агента виртуальной машины Azure, а также парольную фразу и место для хранения выходных данных.
- Импортируется xPSDesiredStateConfiguration DscResource, чтобы можно было использовать xRemoteFile для скачивания файлов из репозитория.
- Создается каталог, где необходимо хранить двоичные файлы.
- Ресурс архива извлекает файлы из ZIP-папки.
- Ресурс пакета установки устанавливает службу Mobility Service из установщика UNIFIEDAGENT.EXE с использованием определенных аргументов (переменные для аргументов необходимо изменить в соответствии с вашей средой).
- Пакет AzureAgent устанавливает агент виртуальной машины Azure, который рекомендуется использовать на каждой виртуальной машине, запущенной в Azure. Это даст возможность добавлять расширения на виртуальную машину после отработки отказа.
- Ресурсы службы обеспечивают, что связанные службы Mobility Service и службы Azure всегда будут запущены.

Сохраните конфигурацию как **ASRMobilityService**.

(Не забудьте заменить CSIP в конфигурации в соответствии с реальным сервером управления, чтобы обеспечить правильное подключение агента. При этом требуется использование правильной парольной фразы.)

## Шаг 3. Отправка в OMS Automation DSC

Так как созданная конфигурация DSC импортирует требуемый модуль ресурсов DSC (xPSDesiredStateConfiguration), необходимо импортировать этот модуль в OMS Automation, прежде чем передать конфигурацию DSC.

Войдите в учетную запись службы автоматизации и щелкните "Ресурсы-контейнеры" > "Модули" > "Просмотреть коллекцию".

Здесь можно найти модуль и импортировать его в вашу учетную запись.

.![Импорт модуля](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

После этого перейдите на компьютер, где установлены модули Azure RM, и импортируйте созданную конфигурацию DSC.

### Импорт командлетов

В PowerShell войдите в подписку Azure и измените командлеты в соответствии со своей средой. Запишите данные своей учетной записи автоматизации в переменную.
```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Сначала передайте конфигурацию в OMS Automation DSC, используя следующий командлет.

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

Теперь нужно скомпилировать конфигурацию в OMS Automation DSC, чтобы можно было начать регистрацию узлов в ней.

### Компиляция конфигурации в OMS Automation DSC

Для этого нужно выполнить следующий командлет.

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Это может занять несколько минут, так как конфигурация фактически развертывается в размещенной службе извлечения DSC.

После завершения можно получить сведения о задании, используя PowerShell (Get-AzureRmAutomationDscCompilationJob) или перейдя на сайт portal.azure.com.

![Получение задания](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Конфигурация DSC успешно опубликована и передана в OMS Automation DSC.

## Шаг 4. Подключение компьютеров к OMS Automation DSC
*Для выполнения этого сценария требуется, чтобы на компьютерах с Windows была установлена последняя версия WMF. Вы можете скачать и установить правильную версию для вашей платформы, перейдя по этому URL-адресу: https://www.microsoft.com/download/details.aspx?id=50395*.

Теперь вы создадите метаконфигурацию для DSC, которая будет применена к узлам. Чтобы сделать это, необходимо получить URL-адрес конечной точки и первичный ключ для выбранной учетной записи службы автоматизации в Azure.

Эти значения можно найти в колонке "Все параметры" (раздел "Ключи") учетной записи службы автоматизации.

![Значения ключей](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

В этом примере есть физический сервер Windows Server 2012 R2, который необходимо защитить с помощью OMS Site Recovery.

Прежде чем связать сервер с конечной точкой Automation DSC, рекомендуется проверить все отложенные операции переименования файлов в реестре, так как ожидающая перезагрузка может препятствовать выполнению установки.

### Проверка всех ожидающих операций переименования файлов в реестре

Чтобы убедиться в отсутствии ожидающей перезагрузки на сервере, выполните следующий командлет:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Если отображается пустое окно, можно продолжить процедуру. В противном случае эту проблему нужно решить, перезагрузив сервер в период обслуживания.

Чтобы применить конфигурацию на сервере, запустите интегрированную среду сценариев PowerShell и выполните приведенный ниже сценарий. По сути это локальная конфигурация DSC, которая сообщит механизму диспетчера локальных конфигураций о необходимости регистрации в службе OMS Automation DSC и получения конкретной конфигурации (ASRMobilityService.localhost).

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

Эта конфигурация настроит диспетчер локальных конфигураций для регистрации в OMS Automation DSC, а также настроит работу механизма в случае отклонений конфигурации (ApplyAndAutoCorrect) и в случае необходимости перезагрузки (продолжение выполнения конфигурации позже).

После этого узел начнет регистрацию в Automation DSC.

![Регистрация узла](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Теперь зарегистрированный узел отображается на портале portal.azure.com.

![Регистрация узла](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Чтобы убедиться, что сервер зарегистрирован надлежащим образом, выполните на нем следующий командлет PowerShell.

```powershell
Get-DscLocalConfigurationManager
```

Чтобы проверить состояние после извлечения и применения конфигурации на сервере, выполните следующую команду:

```powershell
Get-DscConfigurationStatus
```

Результат показывает, что сервер успешно извлек конфигурацию.

.![Регистрация узла](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Кроме того, в программе установки службы Mobility Service есть собственный журнал, расположенный в пути <SystemDrive>\\ProgramData\\ASRSetupLogs.

Вот и все! Служба Mobility Service успешно развернута и зарегистрирована на компьютере, который необходимо защитить с помощью Site Recovery. Благодаря DSC можно быть уверенными, что необходимые службы всегда будут работать.

![Регистрация узла](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

После того как сервер управления определит это, можно приступить к настройке защиты и включить репликацию на компьютере с помощью Site Recovery.

## Использование DSC в отключенных средах

Даже если компьютеры не подключены к Интернету, DSC по-прежнему поможет развернуть и настроить службу Mobility Service для рабочих нагрузок, которые необходимо защитить.

Вы можете создать экземпляр опрашивающего сервера DSC в своей среде (тем самым обеспечив те же возможности, которые предоставляет OMS Automation DSC), где клиенты смогут извлекать конфигурацию после регистрации в конечной точке DSC. Можно также использовать принудительную отправку. При этом конфигурацию DSC необходимо вручную отправить на компьютеры (локально или удаленно).

Обратите внимание, что в этом примере добавлен параметр для имени компьютера, а удаленные файлы теперь находятся в удаленном общем ресурсе. Он должен быть доступен компьютерам, для которых необходимо включить защиту. В конце этого сценария применяется конфигурация, после чего применяется конфигурация DSC на целевом компьютере.

### Предварительные требования

· Установленный модуль xPSDesiredStateConfiguration PowerShell.

Для компьютеров Windows с установленным компонентом WMF 5.0 можно установить модуль xPSDesiredStateConfiguration, просто выполнив на целевых компьютерах следующий командлет:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

Можно также скачать и сохранить модуль, если понадобиться развернуть его на компьютерах Windows с компонентом WMF 4.0. Для этого запустите на компьютере, где есть PowerShellGet (WMF 5.0), следующий командлет:

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

На компьютерах с WMF 4.0 установите следующее обновление:

.https://www.microsoft.com/download/details.aspx?id=40749

Приведенную ниже конфигурацию можно применить к компьютерам Windows с WMF 5.0 и 4.0.

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

Если необходимо создать экземпляр собственного опрашивающего сервера DSC в корпоративной сети, для имитации тех же возможностей, которые предоставляет OMS Automation DSC, см. следующее руководство: https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## Развертывание конфигурации DSC с помощью шаблона Azure Resource Manager (необязательно)

Вы этой статье были рассмотрены способы создания конфигурации DSC для автоматического развертывания службы Mobility Service и агента виртуальной машины Azure, а также было уделено внимание тому, как обеспечить их работу на компьютерах, которые необходимо защитить. Вдобавок у нас также есть шаблон Azure Resource Manager## ##. С его помощью можно развернуть эту конфигурацию DSC в новой или имеющейся учетной записи службы автоматизации Azure и создать ресурсы службы автоматизации, в которых будут содержаться переменные для среды, с использованием входных параметров в шаблоне.

После развертывания см. шаг 4 этого руководства, чтобы подключить компьютеры.

Шаблон выполнит следующее:

· использует имеющуюся или создаст учетную запись службы автоматизации OMS;

· примет входные параметры для:

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


Приведенные выше действия будут выполнены в правильном порядке, чтобы вы могли легко настроить защиту компьютеров.

Шаблон с инструкциями по развертыванию находится по адресу:

.https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

Развертывание с использованием PowerShell:

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

## Дальнейшие действия:

После развертывания агентов службы Mobility Service можно [включить репликацию](site-recovery-vmware-to-azure.md#step-6-replicate-applications) для виртуальных машин.

<!---HONumber=AcomDC_0824_2016-->