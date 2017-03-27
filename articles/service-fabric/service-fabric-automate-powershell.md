---
title: "Автоматизация управления приложениями Azure Service Fabric | Документация Майкрософт"
description: "Развертывание, обновление, тестирование и удаление приложений Service Fabric с помощью PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: f03f4294-571d-4262-8a77-cc8b481b959d
ms.service: service-fabric
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 59133d11d2d66daa44f28ef5b9aa3aae92e56a1e
ms.lasthandoff: 03/15/2017


---
# <a name="automate-the-application-lifecycle-using-powershell"></a>Автоматизация жизненного цикла приложения с помощью PowerShell
Многие аспекты [жизненного цикла приложения Service Fabric](service-fabric-application-lifecycle.md) можно автоматизировать.  В этой статье показано, как использовать PowerShell для автоматизации типовых задач по развертыванию, обновлению, удалению и тестированию приложений Service Fabric.  Также доступны управляемые API и API HTTP для управления приложениями. Дополнительные сведения см. в статье [Жизненный цикл приложения Service Fabric](service-fabric-application-lifecycle.md).  

## <a name="prerequisites"></a>Предварительные требования
Прежде чем переходить к задачам в этой статье, нужно сделать следующее:

* Ознакомьтесь с основными понятиями Service Fabric, описанными в статье [Технический обзор платформы Service Fabric](service-fabric-technical-overview.md).
* Выполните инструкции по установке, описанные в статье [Подготовка среды разработки](service-fabric-get-started.md). При выполнении этих инструкций также будет установлен модуль PowerShell для **ServiceFabric**.
* [Включение сценариев PowerShell](service-fabric-get-started.md#enable-powershell-script-execution)
* Запустите локальный кластер.  Откройте новое окно PowerShell с правами администратора и запустите сценарий установки кластера из папки пакета SDK: `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
* Перед выполнением команд PowerShell, описанных в этой статье, вам необходимо подключиться к локальному кластеру Service Fabric с помощью команды [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster): `Connect-ServiceFabricCluster localhost:19000`
* Следующие задачи требуют развертывания пакета приложения версии&1; и пакета приложения версии&2; для обновления. Скачайте [пример приложения **WordCount**](http://aka.ms/servicefabricsamples) (его можно найти в примерах "Приступая к работе"). Соберите и упакуйте приложение в Visual Studio (щелкнув **WordCount** в обозревателе решений правой кнопкой мыши и выбрав **Упаковать**). Скопируйте пакет версии&1; из `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` в `C:\Temp\WordCount`. Скопируйте `C:\Temp\WordCount` в `C:\Temp\WordCountV2` для создания пакета приложения версии&2; для обновления. Откройте `C:\Temp\WordCountV2\ApplicationManifest.xml` в текстовом редакторе. В элементе **ApplicationManifest** измените значение атрибута **ApplicationTypeVersion** с "1.0.0" на "2.0.0", чтобы обновить версию приложения. Сохраните измененный файл ApplicationManifest.xml.

## <a name="task-deploy-a-service-fabric-application"></a>Задача: развернуть приложение Service Fabric
Собрав и упаковав приложение (или загрузив пакет приложения), можно развернуть приложение в локальном кластере Service Fabric. Развертывание включает отправку пакета приложения, регистрацию типа приложения и создание экземпляра приложения. В этом разделе приводятся инструкции по развертыванию нового приложения в кластере.

### <a name="step-1-upload-the-application-package"></a>Шаг 1. Отправка пакета приложения
Отправка пакета приложения в хранилище образов означает, что он помещается в расположение, доступное внутренним компонентам Service Fabric.  Пакет приложения содержит необходимый манифест приложения, манифесты служб и пакеты данных, конфигурации и кода для создания экземпляров приложения и служб. Если вы хотите проверить пакет приложения локально, используйте командлет [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage).  Команда [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) передает пакет. Например:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Шаг 2. Регистрация пакета приложения
При регистрации пакета приложения его тип и версия, объявленные в манифесте приложения, становятся доступными для использования. Система считывает содержимое пакета, отправленного на шаге 1, проверяет пакет (это эквивалентно локальному выполнению команды [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage)), обрабатывает содержимое пакета и копирует обработанный пакет во внутреннее системное расположение.  Выполните командлет [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCount
```
Чтобы просмотреть все типы приложений, зарегистрированных в кластере, запустите командлет [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype):

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Шаг 3. Создание экземпляра приложения
Для создания экземпляра приложения вы можете использовать любую версию типа приложения, успешно зарегистрированную с помощью команды [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication). Имя каждого приложения объявляется во время развертывания, должно начинаться со схемы **fabric:** и быть уникальным для каждого экземпляра приложения. Имя и версия типа приложения объявляются в файле **ApplicationManifest.xml** пакета приложения. Если в манифесте приложения для конкретного его типа были определены используемые по умолчанию службы, то они также будут созданы.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Команда [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) выводит список всех успешно созданных экземпляров приложения вместе с их общим состоянием. Команда [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) выводит список всех экземпляров службы, успешно созданных в рамках конкретного экземпляра приложения. Этот список содержит службы по умолчанию (при наличии).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Задача: обновить приложение Service Fabric
Развернутое ранее приложение Service Fabric можно обновить с помощью обновленного пакета приложения. Данная задача обновляет приложение WordCount, развернутое при выполнении задачи по развертыванию приложения Service Fabric. Дополнительные сведения см. в статье [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

Для простоты в этом примере в пакете приложения WordCount версии&2;, созданном на этапе предварительных требований, был обновлен только номер версии. Более реалистичный сценарий будет включать в себя обновление файлов кода, конфигурации или данных службы и последующую сборку и упаковку приложения с обновленными номерами версий.  

### <a name="step-1-upload-the-updated-application-package"></a>Шаг 1. Отправка обновленного пакета приложения
Приложение WordCount версии&1; готово для обновления. Открыв окно PowerShell с правами администратора и введя команду [**Get-ServiceFabricApplication**](/powershell/servicefabric/vlatest/get-servicefabricapplication), вы увидите, что развернуто приложение WordCount версии 1.0.0.  

Теперь скопируйте обновленный пакет приложения в хранилище образов (место, в которое Service Fabric сохраняет пакеты приложений). Параметр **ApplicationPackagePathInImageStore** информирует структуру служб о том, где находится пакет приложения. Следующая команда копирует пакет приложения **WordCountV2** в хранилище образов.  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Шаг 2. Регистрация обновленного пакета приложения
Далее необходимо зарегистрировать новую версию приложения в Service Fabric. Для этого выполните командлет [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Шаг 3. Запуск обновления
При обновлении приложения можно изменить различные параметры, время ожидания и критерий оценки работоспособности. Дополнительные сведения см. в статьях [Параметры обновления приложений](service-fabric-application-upgrade-parameters.md) и [Обновление приложения Service Fabric](service-fabric-application-upgrade.md). После обновления все службы и экземпляры должны быть *работоспособны*.  Установите для атрибута **HealthCheckStableDuration** значение 60 секунд (чтобы все службы были работоспособны не менее 20 секунд перед переходом процесса обновления к другому домену обновления).  Кроме того, укажите для атрибута **UpgradeDomainTimeout** значение 1200 секунд, а для атрибута **UpgradeTimeout** — 3000 секунд. И наконец, установите для атрибута **UpgradeFailureAction** значение **rollback**, чтобы Service Fabric выполняла откат приложения до предыдущей версии при возникновении любых неполадок во время обновления.

Теперь вы можете запустить обновление приложения, выполнив командлет [Start-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Обратите внимание на то, что имя приложения совпадает с именем приложения развернутой ранее версии&1;.0.0 (fabric:/WordCount). Структура служб использует это имя, чтобы распознать приложение, которое будет обновлено. Если у вас установлены слишком малые значения времени ожидания, может возникнуть сообщение об ошибке времени ожидания, в котором будет обозначена возникшая проблема. См. статью [Устранение неполадок при обновлениях приложений](service-fabric-application-upgrade-troubleshooting.md) или увеличьте время ожидания.

### <a name="step-4-check-upgrade-progress"></a>Шаг 4. Проверка хода обновления
Вы можете следить за обновлением приложения с помощью [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) или командлета [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Через несколько минут командлет [Get ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) покажет, что все домены обновления обновлены (обновление завершено).

## <a name="task-test-a-service-fabric-application"></a>Задача: протестировать приложение Service Fabric
Чтобы создавать высококачественные службы, разработчики должны иметь возможность вызывать сбои в ненадежных инфраструктурах, чтобы проверять стабильность работы своих служб. Платформа Service Fabric позволяет разработчикам вызывать сбои и тестировать службы при наличии последних с помощью сценариев хаотического тестирования и отработки отказа.  Дополнительные сведения см. в статье [Общие сведения о службе анализа сбоев](service-fabric-testability-overview.md).

### <a name="step-1-run-the-chaos-test-scenario"></a>Шаг 1. Выполнение сценария хаотического тестирования
Сценарий хаотического тестирования создает сбои в масштабах всего кластера Service Fabric. Этот сценарий позволяет всего за несколько часов проработать ошибки, которые обычно наблюдаются в течение многих месяцев или лет. Сочетание сбоев с чередованием и сбоев с высокой частотой возникновения позволяет находить проблемы, которые в противном случае были бы упущены. В следующем примере сценарий хаотического тестирования запускается на 60 минут.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Шаг 2. Выполнение сценария отработки отказа
Сценарий тестовой отработки отказа задействует не весь кластер, а отдельный раздел, не затрагивая остальные службы. При выполнении этого сценария для проверки службы на фоне запущенной бизнес-логики повторяется последовательность моделируемых сбоев. Ошибка при проверке службы указывает на проблему, которую необходимо дополнительно изучить. При отработке отказа одновременно вызывается только один сбой, в то время как в процессе хаотического тестирования могут вызываться сразу несколько сбоев. Следующий пример запускает тест отработки отказа в течение 60 минут для службы fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Задача: удалить приложение Service Fabric
Вы можете удалить экземпляр развернутого приложения, убрать подготовленный тип приложения из кластера или удалить пакет приложения из ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Шаг 1. Удаление экземпляра приложения
Экземпляр приложения, который больше не требуется, можно удалить без возможности восстановления с помощью командлета [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication). При этом все службы, относящиеся к этому приложению, и все их состояния также будут удалены. Эта операция необратима, и вы не сможете восстановить состояние приложения.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Шаг 2. Отмена регистрации типа приложения
Если определенная версия типа приложения больше не требуется, отмените ее регистрацию с помощью командлета [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype). Отмена регистрации неиспользуемых типов помогает освободить пространство, занимаемое в хранилище образов пакетом приложения. Регистрацию типа приложения можно отменить в том случае, если на его основе не были созданы экземпляры приложений и нет ссылающихся на него незавершенных обновлений приложений.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Шаг 3. Удаление пакета приложения
После отмены регистрации типа приложения пакет приложения можно удалить из хранилища образов с помощью командлета [Remove-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/remove-servicefabricapplicationpackage).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Дальнейшие действия
[Жизненный цикл приложения Service Fabric](service-fabric-application-lifecycle.md)

[Развертывание приложения](service-fabric-deploy-remove-applications.md)

[Обновление приложения](service-fabric-application-upgrade.md)

[Командлеты Azure Service Fabric](/powershell/servicefabric/vlatest/servicefabric)


