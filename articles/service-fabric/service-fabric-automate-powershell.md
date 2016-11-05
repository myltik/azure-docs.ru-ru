---
title: Автоматизация управления приложениями Service Fabric с помощью PowerShell | Microsoft Docs
description: Развертывание, обновление, тестирование и удаление приложений Service Fabric с помощью PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''

ms.service: service-fabric
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/25/2016
ms.author: ryanwi

---
# Автоматизация жизненного цикла приложения с помощью PowerShell
Многие аспекты [жизненного цикла приложения Service Fabric](service-fabric-application-lifecycle.md) можно автоматизировать. В этой статье показано, как использовать PowerShell для автоматизации типовых задач по развертыванию, обновлению, удалению и тестированию приложений Service Fabric. Также доступны управляемые API и API HTTP для управления приложениями. Дополнительные сведения см. в разделе о [жизненном цикле приложения](service-fabric-application-lifecycle.md).

## Предварительные требования
Прежде чем переходить к задачам в этой статье, нужно сделать следующее:

* Ознакомьтесь с основными понятиями Service Fabric, описанными в статье [Технический обзор платформы Service Fabric](service-fabric-technical-overview.md).
* Выполните инструкции по установке, описанные в статье [Установка среды выполнения, пакета SDK и инструментов](service-fabric-get-started.md). При выполнении этих инструкций также будет установлен модуль PowerShell для **ServiceFabric**.
* [Включение сценариев PowerShell](service-fabric-get-started.md#enable-powershell-script-execution)
* Запустите локальный кластер. Откройте новое окно PowerShell с правами администратора и запустите сценарий установки кластера из папки пакета SDK: `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
* Перед выполнением команд PowerShell, описанных в этой статье, необходимо подключиться к локальному кластеру Service Fabric с помощью команды [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx): `Connect-ServiceFabricCluster localhost:19000`
* Следующие задачи требуют развертывания пакета приложения версии 1 и пакета приложения версии 2 для обновления. Скачайте [пример приложения **WordCount**](http://aka.ms/servicefabricsamples) (его можно найти в примерах "Приступая к работе"). Соберите и упакуйте приложение в Visual Studio (щелкнув **WordCount** в обозревателе решений правой кнопкой мыши и выбрав **Упаковать**). Скопируйте пакет версии 1 из `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` в `C:\Temp\WordCount`. Скопируйте `C:\Temp\WordCount` в `C:\Temp\WordCountV2` для создания пакета приложения версии 2 для обновления. Откройте `C:\Temp\WordCountV2\ApplicationManifest.xml` в текстовом редакторе. В элементе **ApplicationManifest** измените значение атрибута **ApplicationTypeVersion** с "1.0.0" на "2.0.0", чтобы обновить версию приложения. Сохраните измененный файл ApplicationManifest.xml.

## Задача: развернуть приложение Service Fabric
Собрав и упаковав приложение (или загрузив пакет приложения), можно развернуть приложение в локальном кластере Service Fabric. Развертывание включает отправку пакета приложения, регистрацию типа приложения и создание экземпляра приложения. В этом разделе приводятся инструкции по развертыванию нового приложения в кластере.

### Шаг 1. Отправка пакета приложения
Отправка пакета приложения в хранилище образов означает, что он помещается в расположение, доступное внутренним компонентам Service Fabric. Пакет приложения содержит необходимый манифест приложения, манифесты служб и пакеты данных, конфигурации и кода для создания экземпляров приложения и служб. Команда [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) передает пакет. Например:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Шаг 2. Регистрация пакета приложения
При регистрации пакета приложения его тип и версия, объявленные в манифесте приложения, становятся доступными для использования. Система считывает содержимое пакета, отправленного на шаге 1, проверяет пакет (это эквивалентно локальному выполнению команды [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx)), обрабатывает содержимое пакета и копирует обработанный пакет во внутреннее системное расположение. Выполните командлет [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx).

```powershell
Register-ServiceFabricApplicationType WordCount
```
Чтобы просмотреть все типы приложений, зарегистрированных в кластере, запустите командлет [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx):

```powershell
Get-ServiceFabricApplicationType
```

### Шаг 3. Создание экземпляра приложения
Для создания экземпляра приложения можно использовать любую версию типа приложения, успешно зарегистрированную с помощью команды [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx). Имя каждого приложения объявляется во время развертывания, должно начинаться со схемы **fabric:** и быть уникальным для каждого экземпляра приложения. Имя и версия типа приложения объявляются в файле **ApplicationManifest.xml** пакета приложения. Если в манифесте приложения для конкретного его типа были определены используемые по умолчанию службы, то они также будут созданы.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Команда [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) выводит список всех успешно созданных экземпляров приложения вместе с их общим состоянием. Команда [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) выводит список всех экземпляров службы, созданных в рамках конкретного экземпляра приложения. Этот список содержит службы по умолчанию (при наличии).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Задача: обновить приложение Service Fabric
Развернутое ранее приложение Service Fabric можно обновить с помощью обновленного пакета приложения. Данная задача обновляет приложение WordCount, развернутое при выполнении задачи по развертыванию приложения Service Fabric. Дополнительные сведения см. в разделе [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

Для простоты в этом примере в пакете приложения WordCount версии 2, созданном на этапе предварительных требований, был обновлен только номер версии. Более реалистичный сценарий будет включать в себя обновление файлов кода, конфигурации или данных службы и последующую сборку и упаковку приложения с обновленными номерами версий.

### Шаг 1. Отправка обновленного пакета приложения
Приложение WordCount версии 1 готово для обновления. Открыв окно PowerShell с правами администратора и введя команду [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), вы увидите, что развернуто приложение WordCount версии 1.0.0.

Теперь скопируйте обновленный пакет приложения в хранилище образов (место, в которое Service Fabric сохраняет пакеты приложений). Параметр **ApplicationPackagePathInImageStore** информирует структуру служб о том, где находится пакет приложения. Следующая команда копирует пакет приложения **WordCountV2** в хранилище образов.

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### Шаг 2. Регистрация обновленного пакета приложения
Далее необходимо зарегистрировать новую версию приложения в Service Fabric, выполнив для этого командлет [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### Шаг 3. Запуск обновления
При обновлении приложения можно изменить различные параметры, время ожидания и критерий оценки работоспособности. Дополнительные сведения см. в статьях [Параметры обновления приложений](service-fabric-application-upgrade-parameters.md) и [Процесс обновления](service-fabric-application-upgrade.md). После обновления все службы и экземпляры должны быть *работоспособны*. Установите для атрибута **HealthCheckStableDuration** значение 60 секунд (чтобы все службы были работоспособны не менее 20 секунд перед переходом процесса обновления к другому домену обновления). Кроме того, укажите для атрибута **UpgradeDomainTimeout** значение 1200 секунд, а для атрибута **UpgradeTimeout** — 3000 секунд. И наконец, установите атрибут **UpgradeFailureAction** в значение **rollback**, чтобы Service Fabric выполняла откат приложения до предыдущей версии при возникновении любых неполадок во время обновления.

Теперь можно запустить обновление приложения, выполнив командлет [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Обратите внимание на то, что имя приложения совпадает с именем приложения развернутой ранее версии 1.0.0 (fabric:/WordCount). Структура служб использует это имя, чтобы распознать приложение, которое будет обновлено. Если у вас установлены слишком малые значения времени ожидания, может возникнуть сообщение об ошибке времени ожидания, в котором будет обозначена возникшая проблема. См. статью [Устранение неполадок при обновлениях приложений](service-fabric-application-upgrade-troubleshooting.md) или увеличьте время ожидания.

### Шаг 4. Проверка хода обновления
За обновлением приложения можно следить с помощью [обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md) или командлета [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Через несколько минут командлет [Get ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) покажет, что все домены обновления обновлены (обновление завершено).

## Задача: протестировать приложение Service Fabric
Чтобы создавать высококачественные службы, разработчики должны иметь возможность вызывать сбои в ненадежных инфраструктурах, чтобы проверять стабильность работы своих служб. Платформа Service Fabric позволяет разработчикам вызывать сбои и тестировать службы при наличии последних с помощью сценариев хаотического тестирования и отработки отказа. Дополнительные сведения см. в статье [Обзор Testability](service-fabric-testability-overview.md).

### Шаг 1. Выполнение сценария хаотического тестирования
Сценарий хаотического тестирования создает сбои в масштабах всего кластера Service Fabric. Этот сценарий позволяет всего за несколько часов проработать ошибки, которые обычно наблюдаются в течение многих месяцев или лет. Сочетание сбоев с чередованием и сбоев с высокой частотой возникновения позволяет находить проблемы, которые в противном случае были бы упущены. В следующем примере сценарий хаотического тестирования запускается на 60 минут.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Шаг 2. Выполнение сценария отработки отказа
Сценарий тестовой отработки отказа задействует не весь кластер, а отдельный раздел, не затрагивая остальные службы. При выполнении этого сценария для проверки службы на фоне запущенной бизнес-логики повторяется последовательность моделируемых сбоев. Ошибка при проверке службы указывает на проблему, которую необходимо дополнительно изучить. При отработке отказа одновременно вызывается только один сбой, в то время как в процессе хаотического тестирования могут вызываться сразу несколько сбоев. Следующий пример запускает тест отработки отказа в течение 60 минут для службы fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Задача: удалить приложение Service Fabric
Вы можете удалить экземпляр развернутого приложения, убрать подготовленный тип приложения из кластера или удалить пакет приложения из ImageStore.

### Шаг 1. Удаление экземпляра приложения
Экземпляр приложения, который больше не требуется, можно удалить без возможности восстановления с помощью командлета [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx). При этом все службы, относящиеся к этому приложению, и все их состояния также будут удалены. Эта операция необратима, и вы не сможете восстановить состояние приложения.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### Шаг 2. Отмена регистрации типа приложения
Если определенная версия типа приложения больше не требуется, отмените ее регистрацию с помощью командлета [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx). Отмена регистрации неиспользуемых типов помогает освободить пространство, занимаемое в хранилище образов пакетом приложения. Регистрацию типа приложения можно отменить в том случае, если на его основе не были созданы экземпляры приложений и нет ссылающихся на него незавершенных обновлений приложений.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### Шаг 3. Удаление пакета приложения
После отмены регистрации типа приложения пакет приложения можно удалить из хранилища образов с помощью командлета [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## Дальнейшие действия
[Жизненный цикл приложения Service Fabric](service-fabric-application-lifecycle.md)

[Развертывание приложения](service-fabric-deploy-remove-applications.md)

[Обновление приложения](service-fabric-application-upgrade.md)

[Командлеты Azure Service Fabric](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Командлеты Azure Service Fabric Testability](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=AcomDC_0831_2016-->