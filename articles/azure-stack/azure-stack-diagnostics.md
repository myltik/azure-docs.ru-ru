---
title: "Диагностика в Azure Stack"
description: "Как собирать файлы журналов для диагностики в Azure Stack"
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: fdbf9b1b77c2c64b3ebfcdbc5463916f317e4881
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Средства диагностики Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*
 
Azure Stack — это большая коллекция компонентов, взаимодействующих между собой. Все эти компоненты создают свои собственные уникальные журналы. Это может усложнить диагностику проблем, особенно в случае с ошибками, поступающими из нескольких взаимодействующих компонентов Azure Stack. 

Наши средства диагностики обеспечивают простоту и эффективность механизма сбора журналов. На следующей схеме показана работа средств сбора журналов в Azure Stack.

![Инструменты диагностики Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Сборщик трассировки
 
Сборщиком трассировки включен по умолчанию и постоянно работает в фоновом режиме для сбора всех журналов трассировки событий Windows (ETW) из служб компонентов Azure Stack. Журналы трассировки событий Windows хранятся в общей локальной папке в течение пяти дней. По достижении этого ограничения самые старые файлы удаляются и создаются новые. Максимальный размер файла по умолчанию составляет 200 МБ. Проверка размера выполняется каждые 2 минуты, и если размер текущего файла превышает 200 МБ, он сохраняется и создается новый. Кроме того, есть ограничение до 8 ГБ для общего размера файлов, создаваемых во время сеанса событий. 

## <a name="log-collection-tool"></a>Средство сбора журналов
 
Командлет PowerShell **Get-AzureStackLog** можно использовать для сбора журналов из всех компонентов в среде Azure Stack. Она сохраняет их в ZIP-файлы в расположении, определяемом пользователем. Если сотрудникам службы технической поддержки Azure Stack понадобятся журналы для устранения проблемы, они могут попросить вас запустить это средство.

> [!CAUTION]
> Эти файлы журналов могут содержать личные сведения. Учитывайте это, прежде чем открыто публиковать какие-либо файлы журналов.
 
Ниже приведены несколько примеров собираемых типов журналов:
*   **журналы развертывания Azure Stack**;
*   **журналы событий Windows**;
*   **журналы Panther**.
*   **журналы кластера**;
*   **журналы диагностики хранилища**;
*   **журналы трассировки событий Windows**.

Эти файлы собираются и сохраняются в файловом ресурсе сборщиком трассировки. Затем при необходимости их можно будет собрать с помощью командлета PowerShell **Get-AzureStackLog**.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Выполнение командлета Get-AzureStackLog в системе комплекта SDK Azure Stack (ASDK)
1. Войдите на узел как **AzureStack\CloudAdmin**.
2. Откройте окно PowerShell от имени администратора.
3. Выполните командлет PowerShell **Get-AzureStackLog**.

**Примеры**

  Соберите все журналы для всех ролей:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Соберите журналы из ролей VirtualMachines и BareMetal:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Соберите журналы из ролей VirtualMachines и BareMetal с фильтром по дате для файлов журнала за последние 8 часов:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Соберите журналы из ролей VirtualMachines и BareMetal с фильтром по дате для файлов журнала за период времени между последними 8 и 2 часами:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>Выполнение командлета Get-AzureStackLog в интегрированной системе Azure Stack

Чтобы запустить средство сбора журналов в интегрированной системе, необходимо иметь доступ к привилегированной конечной точке (PEP). Ниже приведен пример скрипта, который можно запустить с помощью PEP для сбора журналов в интегрированной системе:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- При сборе журналов из PEP укажите для параметра **OutputPath** расположение на компьютере узла отслеживания жизненного цикла оборудования (HLH). Кроме того, расположение должно быть зашифровано.
- Параметры **OutputSharePath** и **OutputShareCredential** являются необязательными и используются при передаче журналов во внешнюю общую папку. Используйте эти параметры *в дополнение* к **OutputPath**. Если параметр **OutputPath** не указан, инструмент сбора журналов будет использовать для хранения системный диск виртуальной машины PEP. Это может вызвать ошибку скрипта, так как место на диске ограничено.
- Как показано в предыдущем примере, параметры **FromDate** и **ToDate** можно использовать для сбора журналов за конкретный период времени. Это может быть полезным для таких сценариев, как сбор журналов после применения пакета обновления в интегрированной системе.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Рекомендации по настройке параметров для ASDK и интегрированных систем

- Если параметры **FromDate** и **ToDate** не указаны, по умолчанию журналы собираются за последние четыре часа.
- Задать время ожидания для сбора журналов можно с помощью параметра **TimeOutInMinutes**. По умолчанию для него задано значение 150 (2,5 часа).

- Сейчас можно использовать параметр **FilterByRole**, чтобы отфильтровать сбор журналов по следующим ролям.

   |   |   |   |
   | - | - | - |
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | ADFS                    | ASAppGateway           | BareMetal          |
   | BRP                     | CA                     | CPI                |
   | CRP                     | DeploymentMachine      | DHCP               |
   | Домен                  | ECE                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | Шлюз                 | HealthMonitoring       | HRP                |   
   | IBC                     | InfraServiceController | KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | SRP                     | Хранилище                | StorageController  |
   | URP                     | UsageBridge            | VirtualMachines    |  
   | WAS                     | WASPUBLIC              | WDS                |


### <a name="collect-logs-using-a-graphical-user-interface"></a>Сбор журналов с помощью графического пользовательского интерфейса
Вместо того, чтобы указывать обязательные параметры командлета Get-AzureStackLog для получения журналов Azure Stack, можно также использовать доступные инструменты Azure Stack с открытым кодом, размещенные в главном репозитории GitHub инструментов Azure Stack по адресу http://aka.ms/AzureStackTools.

Сценарий PowerShell **ERCS_AzureStackLogs.ps1** хранится в репозитории GitHub инструментов и регулярно обновляется. Чтобы обеспечить наличие самой последней доступной версии, необходимо скачать ее по адресу http://aka.ms/ERCS. Если запустить его из сеанса администратора PowerShell, этот сценарий подключается к привилегированной конечной точке и выполняет командлет Get-AzureStackLog с указанными параметрами. Если параметры не указаны, скрипт по умолчанию ожидает ввода параметров через графический пользовательский интерфейс.

Чтобы узнать больше о скрипте PowerShell ERCS_AzureStackLogs.ps1, вы можете ознакомиться с [коротким видео](https://www.youtube.com/watch?v=Utt7pLsXEBc) или просмотреть [файл сведений](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) для сценария, который находится в репозитории GitHub инструментов Azure Stack. 

### <a name="additional-considerations"></a>Дополнительные замечания

* Выполнение команды займет некоторое время в зависимости от того, по какой роли (ролям) собираются журналы. К ключевым факторам также относится промежуток времени, указанный для сбора журналов, а также количество узлов в среде Azure Stack.
* После завершения сбора журналов проверьте новую папку, созданную в расположении, которое задано в параметре **OutputPath**, указанном в команде.
* Журналы каждой роли хранятся в отдельных ZIP-файлах. В зависимости от размера собранных журналов роли могут быть разделены на несколько ZIP-файлов. Если необходимо распаковать все файлы такой роли в одну папку, используйте средство, которое может распаковать в пакетном режиме (например, 7zip). Выберите все ZIP-файлы для роли и щелкните **extract here** (Извлечь сюда). После этого все файлы журналов для этой роли будут распакованы в одну объединенную папку.
* Файл **Get-AzureStackLog_Output.log** также создается в папке, содержащей ZIP-файлы журналов. Этот файл представляет собой журнал выходных данных команды, который можно использовать для устранения неполадок во время сбора журналов.
* Для изучения конкретного сбоя могут понадобиться журналы из нескольких компонентов.
    -   Журналы системы и событий для всех виртуальных машин инфраструктуры собираются в роль *VirtualMachines*.
    -   Журналы системы и событий для всех узлов собираются в роль *BareMetal*.
    -   Журналы событий отказоустойчивого кластера и Hyper-V собираются в роль *Storage*.
    -   Журналы ACS собираются в роли *Storage* и *ACS*.

> [!NOTE]
> К собранным журналам применяются ограничения размера и возраста, так как очень важно обеспечить эффективное использование дискового пространства и не переполнять его журналами. Тем не менее при диагностике проблемы иногда могут понадобиться журналы, которые уже удалены из-за этих ограничений. Поэтому **настоятельно рекомендуется** разгружать журналы во внешнее дисковое пространство (учетная запись хранения в Azure, дополнительное локальное устройство хранения данных и т. д.) каждые 8–12 часов и хранить их там 1–3 месяца в зависимости от ваших требований. Кроме того, это хранилище должно быть зашифровано.

## <a name="next-steps"></a>Дополнительная информация
[Microsoft Azure Stack troubleshooting (Устранение неполадок, связанных с Microsoft Azure Stack)](azure-stack-troubleshooting.md)
