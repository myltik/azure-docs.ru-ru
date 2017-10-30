---
title: "Диагностика в Azure Stack"
description: "Как собирать файлы журналов для диагностики в Azure Stack"
services: azure-stack
author: adshar
manager: byronr
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 10/2/2017
ms.author: adshar
ms.openlocfilehash: 9b1fbbf63ddd8bac2c1a76bbcd5daca69e2513f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-diagnostics-tools"></a>Средства диагностики Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*
 
Azure Stack — это большая коллекция компонентов, взаимодействующих между собой. Все эти компоненты создают свои собственные уникальные журналы. Это может усложнить диагностику проблем, особенно в случае с ошибками, поступающими из нескольких взаимодействующих компонентов Azure Stack. 

Наши средства диагностики обеспечивают простоту и эффективность механизма сбора журналов. На следующей схеме показана работа средств сбора журналов в Azure Stack.

![Средства сбора журналов](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>Сборщик трассировки
 
Сборщик трассировки включен по умолчанию. Он работает в фоновом режиме и собирает все журналы трассировки событий Windows из служб компонентов в Azure Stack и сохраняет их в общей локальной папке. 

Ниже приведены важные сведения о сборщике трассировки.
 
* Это средство работает непрерывно с ограничениями размера по умолчанию. Максимальный размер по умолчанию, допустимый для каждого файла (200 МБ), — **не** пороговое значение. Периодически происходит проверка размера (сейчас каждые 2 минуты), и если размер текущего файла превышает 200 МБ, он сохраняется и создается новый. Кроме того, есть ограничение до 8 ГБ (настраиваемое) на общий размер файлов, создаваемых во время сеанса событий. По достижении этого ограничения самые старые файлы удаляются и создаются новые.
* Для журналов установлено ограничение на возраст в размере 5 дней. Его можно настроить. 
* Свойства конфигурации трассировки для каждого компонента определены в JSON-файле. JSON-файлы хранятся в `C:\TraceCollector\Configuration`. При необходимости эти файлы можно редактировать, чтобы изменить ограничения размера и возраста собираемых журналов. Чтобы изменения в этих файлах вступили в силу, требуется перезапустить службу *Сборщик трассировки Microsoft Azure Stack*.
* В следующем примере представлен JSON-файл конфигурации трассировки для операций FabricRingServices из виртуальной машины XRP: 

```
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**

    Этот параметр контролирует возраст хранящихся файлов. Старые файлы журнала удаляются.
* **MaxSizeInMB**

    Этот параметр контролирует пороговое значение размера для одного файла. По его достижении создается новый ETL-файл.
* **TotalSizeInMB**

    Этот параметр контролирует общий размер ETL-файлов, созданных во время сеанса событий. Если общий размер файлов превышает значение этого параметра, старые файлы будут удалены.
  
## <a name="log-collection-tool"></a>Средство сбора журналов
 
Команду PowerShell `Get-AzureStackLog` можно использовать для сбора журналов из всех компонентов в среде Azure Stack. Она сохраняет их в ZIP-файлы в расположении, определяемом пользователем. Если работникам нашей службы технической поддержки понадобятся журналы для устранения проблемы, они могут попросить вас запустить это средство.

> [!CAUTION]
> Эти файлы журналов могут содержать личные сведения. Учитывайте это, прежде чем открыто публиковать какие-либо файлы журналов.
 
Ниже приведены несколько примеров собираемых типов журналов:
*   **журналы развертывания Azure Stack**;
*   **журналы событий Windows**;
*   **журналы Panther**.

   Устранение неполадок при создании виртуальной машины:
*   **журналы кластера**;
*   **журналы диагностики хранилища**;
*   **журналы трассировки событий Windows**.

Эти файлы собираются сборщиком трассировки и сохраняются в общей папке, из которой их извлекает команда `Get-AzureStackLog`.
 
**Выполнение команды Get-AzureStackLog в системе комплекта разработки Azure Stack (ASDK)**
1.  Войдите в систему с правами AzureStack\AzureStackAdmin на узле.
2.  Откройте окно PowerShell от имени администратора.
3.  Запустите `Get-AzureStackLog`.  

    **Примеры**

    - Соберите все журналы для всех ролей:

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - Соберите журналы из ролей VirtualMachines и BareMetal:

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - Соберите журналы из ролей VirtualMachines и BareMetal с фильтром по дате для файлов журнала за последние 8 часов:

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)`

    - Соберите журналы из ролей VirtualMachines и BareMetal с фильтром по дате для файлов журнала за период времени между последними 8 и 2 часами:

      `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)`

**Выполнение команды Get-AzureStackLog в интегрированной системе Azure Stack**

Чтобы запустить средство сбора журналов в интегрированной системе, необходимо иметь доступ к привилегированной конечной точке (PEP). Ниже приведен пример скрипта, который можно запустить с помощью PEP для сбора журналов в интегрированной системе:

```
$ip = "<IP OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDREESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- При сборе журналов из PEP укажите для параметра `OutputPath` расположение на компьютере HLH. Кроме того, расположение должно быть зашифровано.
- Параметры `OutputSharePath` и `OutputShareCredential` являются необязательными и используются при передаче журналов во внешнюю общую папку. Используйте эти параметры *в дополнение* к `OutputPath`. Если параметр `OutputPath` не указан, средство сбора журналов будет использовать для хранения системный диск виртуальной машины PEP. Это может вызвать ошибку скрипта, так как место на диске ограничено.
- Как показано в предыдущем примере, параметры `FromDate` и `ToDate` можно использовать для сбора журналов за конкретный период времени. Это может быть полезным для таких сценариев, как сбор журналов после применения пакета обновления в интегрированной системе.

**Рекомендации для настройки параметров для системы ASDK и интегрированных систем**

- Если параметры `FromDate` и `ToDate` не указаны, по умолчанию журналы собираются за последние четыре часа.
- Задать время ожидания для сбора журналов можно с помощью параметра `TimeOutInMinutes`. По умолчанию для него задано значение 150 (2,5 часа).

- Сейчас можно использовать параметр `FilterByRole`, чтобы отфильтровать сбор журналов по следующим ролям:

   |   |   |   |
   | - | - | - |
   | `ACSMigrationService`     | `ACSMonitoringService`   | `ACSSettingsService` |
   | `ACS`                     | `ACSFabric`              | `ACSFrontEnd`        |
   | `ACSTableMaster`          | `ACSTableServer`         | `ACSWac`             |
   | `ADFS`                    | `ASAppGateway`           | `BareMetal`          |
   | `BRP`                     | `CA`                     | `CPI`                |
   | `CRP`                     | `DeploymentMachine`      | `DHCP`               |
   |`Domain`                   | `ECE`                    | `ECESeedRing`        |        
   | `FabricRing`              | `FabricRingServices`     | `FRP`                |
   |` Gateway`                 | `HealthMonitoring`       | `HRP`                |               
   | `IBC`                     | `InfraServiceController` | `KeyVaultAdminResourceProvider`|
   | `KeyVaultControlPlane`    | `KeyVaultDataPlane`      | `NC`                 |            
   | `NonPrivilegedAppGateway` | `NRP`                    | `SeedRing`           |
   | `SeedRingServices`        | `SLB`                    | `SQL`                |     
   | `SRP`                     | `Storage`                | `StorageController`  |
   | `URP`                     | `UsageBridge`            | `VirtualMachines`    |  
   | `WAS`                     | `WASPUBLIC`              | `WDS`                |


Дополнительные сведения:

* Выполнение команды займет некоторое время в зависимости от того, по какой роли (ролям) собираются журналы. К ключевым факторам также относится промежуток времени, указанный для сбора журналов, а также количество узлов в среде Azure Stack.
* После завершения сбора журналов проверьте новую папку, созданную в расположении, заданном в параметре `-OutputPath`, указанном в команде.
* Журналы каждой роли хранятся в отдельных ZIP-файлах. В зависимости от размера собранных журналов роли могут быть разделены на несколько ZIP-файлов. Если необходимо распаковать все файлы такой роли в одну папку, используйте средство, которое может распаковать в пакетном режиме (например, 7zip). Выберите все ZIP-файлы для роли и щелкните **extract here** (Извлечь сюда). После этого все файлы журналов для этой роли будут распакованы в одну объединенную папку.
* Файл с именем `Get-AzureStackLog_Output.log` также создается в папке, содержащей ZIP-файлы журналов. Этот файл представляет собой журнал выходных данных команды, который можно использовать для устранения неполадок во время сбора журналов.
* Для изучения конкретного сбоя могут понадобиться журналы из нескольких компонентов.
    -   Журналы системы и событий для всех виртуальных машин инфраструктуры собираются в роль *VirtualMachines*.
    -   Журналы системы и событий для всех узлов собираются в роль *BareMetal*.
    -   Журналы событий отказоустойчивого кластера и Hyper-V собираются в роль *Storage*.
    -   Журналы ACS собираются в роли *Storage* и *ACS*.

> [!NOTE]
> К собранным журналам применяются ограничения размера и возраста, так как очень важно обеспечить эффективное использование дискового пространства и не переполнять его журналами. Тем не менее при диагностике проблемы иногда могут понадобиться журналы, которые уже удалены из-за этих ограничений. Поэтому **настоятельно рекомендуется** разгружать журналы во внешнее дисковое пространство (учетная запись хранения в Azure, дополнительное локальное устройство хранения данных и т. д.) каждые 8–12 часов и хранить их там 1–3 месяца в зависимости от ваших требований. Кроме того, это хранилище должно быть зашифровано.

## <a name="next-steps"></a>Дальнейшие действия
[Microsoft Azure Stack troubleshooting (Устранение неполадок, связанных с Microsoft Azure Stack)](azure-stack-troubleshooting.md)
