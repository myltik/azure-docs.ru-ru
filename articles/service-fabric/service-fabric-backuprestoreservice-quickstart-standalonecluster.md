---
title: Периодическое резервное копирование и восстановление в Azure Service Fabric (предварительная версия) | Документация Майкрософт
description: Используйте функцию периодического резервного копирования и восстановления Service Fabric для защиты приложений от потери данных.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: cfbc0e6ca255bd005bb6e4cc381a9121347fe227
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206051"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Периодическое резервное копирование и восстановление в Azure Service Fabric (предварительная версия)
> [!div class="op_single_selector"]
> * [Кластеры в Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Изолированные кластеры](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric — это платформа распределенных систем, которая упрощает разработку надежных распределенных облачных приложений на основе микрослужб и управление ими. Она позволяет запускать микрослужбы с отслеживанием и без отслеживания состояния. Службы с отслеживанием состояния могут поддерживать изменчивое, авторитетное состояние за пределами запроса и ответа или полной транзакции. Если служба с отслеживанием состояния отключена в течение длительного времени или теряет информацию вследствие аварии, может возникнуть необходимость восстановления до недавней резервной копии состояния для дальнейшего использования службы.

Service Fabric реплицирует состояние на нескольких узлах, обеспечивая высокую доступность службы. Даже в случае сбоя одного узла в кластере служба будет оставаться доступной. Однако в некоторых случаях все же желательно обеспечить устойчивость данных службы к более масштабным сбоям.
 
Например, службе может потребоваться создать резервную копию своих данных для защиты от таких сценариев:
- в случае полной потери всего кластера Service Fabric.
- Полной потери большей части реплик секций служб.
- Если из-за административных ошибок произошло случайное удаление или повреждение состояния. Например, администратор с достаточными привилегиями ошибочно удалил службу.
- Если из-за ошибок в службе происходит повреждение данных. Например, это может произойти, если при обновлении кода службы начинается запись поврежденных данных в надежную коллекцию. В этом случае может потребоваться вернуть код и данные в предыдущее состояние.
- При автономной обработке данных. Автономная обработка данных для бизнес-аналитики, которая происходит отдельно от службы, создающей данные, может быть очень удобной.

Service Fabric предоставляет встроенный API для выполнения [резервного копирования и восстановления](service-fabric-reliable-services-backup-restore.md) на момент во времени. Разработчики приложений могут использовать эти API для периодического резервного копирования состояния службы. Кроме того, если администраторы службы хотят активировать резервное копирование из-за пределов службы в определенный момент время, например перед обновлением приложения, разработчики должны предоставить возможность получения резервной копии (и восстановления) в виде API этой службы. Поддержка резервных копий влечет за собой дополнительные расходы. Например, вы можете создать 5 добавочных резервных копий каждые полчаса, а затем — полную резервную копию. После создания полной резервной копии можно удалить предыдущие добавочные резервные копии. Такой подход требует написания дополнительного кода, что приводит к дополнительным затратам при разработке приложений.

Периодическое резервное копирование данных приложения является основной потребностью для обеспечения процесса управления распределенным приложением и его защиты от потери данных либо длительной недоступности службы. Service Fabric предоставляет дополнительную службу резервного копирования и восстановления, которая позволяет настраивать периодическое резервное копирование надежных служб с отслеживанием состояния (включая службы субъекта) без необходимости написания дополнительного кода. Она также упрощает восстановление ранее сделанных резервных копий. 

> [!NOTE]
> В настоящее время доступна **предварительная версия** функции периодического резервного копирования и восстановления. Она не поддерживается для рабочих нагрузок. 
>

Service Fabric предоставляет набор API для использования функций, связанных с возможностью периодического резервного копирования и восстановления.

- Планирование периодического резервного копирования надежных служб с отслеживанием состояния и Reliable Actors с поддержкой передачи резервных копий во внешние места хранения. Поддерживаемые места хранения
    - Хранилище Azure
    - Файловый ресурс (локальный).
- Перечисление резервных копий.
- Запуск нерегламентированного резервного копирования секции.
- Восстановление секции с помощью предыдущей резервной копии.
- Временная остановка резервного копирования.
- Управление хранением резервных копий (предстоящих).

## <a name="prerequisites"></a>предварительным требованиям
* Кластер Service Fabric с платформой Fabric версии 6.2 и выше. Кластер должен быть установлен на Windows Server. Ознакомьтесь со [статьей](service-fabric-cluster-creation-for-windows-server.md) о процедуре загрузки требуемого пакета.
* Сертификат X.509 для шифрования секретов, необходимых для подключения к хранилищу резервных копий. Ознакомьтесь со [статьей](service-fabric-windows-cluster-x509-security.md) о том, как получить или создать самозаверяющий сертификат X.509.
* Надежное приложения Service Fabric с отслеживанием состояния, созданное с помощью пакета SDK Service Fabric версии 3.0 или выше. Приложение, ориентированное на .Net Core 2.0, нужно создавать с помощью пакета SDK Service Fabric версии 3.1 или выше.

## <a name="enabling-backup-and-restore-service"></a>Включение резервного копирования и восстановления службы
Сначала необходимо включить _службу резервного копирования и восстановления_ в кластере. Получите шаблон для кластера, который требуется развернуть. Вы можете использовать [примеры шаблонов](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Включите _службу резервного копирования и восстановления_ следующим образом:

1. Убедитесь, что для `apiversion` задано значение `10-2017` в файле конфигурации кластера. В противном случае обновите его, как показано во фрагменте кода ниже.

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Включите _службу резервного копирования и восстановления_ , добавив следующий раздел `addonFeatures` после раздела `properties`, как показано во фрагменте кода ниже: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Настройте сертификат X.509 для шифрования учетных данных. Это необходимо, чтобы перед сохранением зашифровать учетные данные (если они имеются), предоставленные для подключения к хранилищу. Настройте сертификат шифрования, добавив следующий раздел `BackupRestoreService` после раздела `fabricSettings`, как показано во фрагменте кода ниже: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. После обновления файла конфигурации кластера с учетом вышеуказанных изменений и дождитесь завершения развертывания или обновления. По завершении _служба резервного копирования и восстановления_ запустится в кластере. Uri этой службы — `fabric:/System/BackupRestoreService`. Она может быть расположена в разделе системных служб в обозревателе Service Fabric. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Включение периодического резервного копирования для надежной службы с отслеживанием состояния и Reliable Actors
Давайте рассмотрим шаги ниже, чтобы включить периодическое резервное копирование для надежной службы с отслеживанием состояния, а также службы Reliable Actors. Предполагается следующее:
- Кластер настроен с помощью _службы резервного копирования и восстановления_.
- Надежная служба с отслеживанием состояния развернута в кластере. В этом кратком руководстве Uri приложения — `fabric:/SampleApp`, а Uri службы с отслеживанием состояния, относящейся к этому приложению, — `fabric:/SampleApp/MyStatefulService`. Эта служба развертывается с одним разделом с идентификатором `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Создание политики архивации

Первым шагом является создание политики архивации, содержащей расписание резервного копирования, сведения о целевом хранилище для данных резервного копирования, имени политики и максимальном количестве добавочных резервных копий, которые необходимо разрешить до запуска полной резервной копии. 

Для хранилища резервных копий создайте общий файловый ресурс и предоставьте ему права ReadWrite для всех компьютеров узла Service Fabric. Пример предполагает наличие папки с именем `BackupStore`, размещенной на `StorageServer`.

Выполните следующий сценарий PowerShell, чтобы вызвать требуемый REST API для создания политики.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Включение периодического резервного копирования
После определения политики резервного копирования, соответствующей требованиям защиты данных приложения, необходимо связать ее с приложением. В зависимости от требований политику резервного копирования можно связать с приложением, службой или секцией.

Выполните следующий сценарий PowerShell для вызова необходимого REST API, чтобы связать политику резервного копирования `BackupPolicy1`, созданную на предыдущем шаге, с приложением `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Проверка работоспособности периодического резервного копирования

После включения резервного копирования на уровне приложения все секции, относящиеся к надежным службам с отслеживанием состояния, а также службам Reliable Actors в рамках приложения, будут периодически получать резервные копии согласно соответствующей политике архивации. 

![Событие работоспособности резервных копий секции][0]

### <a name="list-backups"></a>Перечисление резервных копий

Резервные копии, связанные со всеми секциями, принадлежащими надежным службам с отслеживанием состояния и службам Reliable Actors приложения, можно перечислить с помощью API _GetBackups_. В зависимости от требований резервные копии можно перечислить для приложения, службы или секции.

Выполните следующий сценарий PowerShell, чтобы вызвать API HTTP для перечисления резервных копий, созданных для всех секций внутри приложения `SampleApp`.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Пример выходных данных описанного выше процесса.

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="preview-limitation-caveats"></a>Ограничения и предостережения предварительной версии
- Отсутствие встроенных командлетов PowerShell для платформы Service Fabric.
- Отсутствие поддержки интерфейса командной строки Service Fabric.
- Отсутствие поддержки автоматического удаления ранее созданных резервных копий. Необходимо удаление резервных копий вручную.
- Отсутствие поддержки кластеров Service Fabric в Linux.

## <a name="next-steps"></a>Дальнейшие действия
- [Backup restore REST API reference](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore) (Справочник по REST API службы резервного копирования и восстановления)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

