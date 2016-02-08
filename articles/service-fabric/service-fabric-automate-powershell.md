<properties
	pageTitle="Автоматизация управления приложениями Service Fabric с помощью PowerShell | Microsoft Azure"
	description="Развертывание, обновление, тестирование и удаление приложений Service Fabric с помощью PowerShell."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="ryanwi"/>

# Развертывание, обновление, тестирование и удаление приложений Service Fabric с помощью PowerShell

В этой статье показано, как использовать PowerShell для автоматизации типовых задач по развертыванию, обновлению, удалению и тестированию приложений Service Fabric.

## Предварительные требования

Прежде чем переходить к описанным в этой статье задачам, выполните [установку среды выполнения, пакета SDK и инструментов](service-fabric-get-started.md), при которой также будут установлены модули **ServiceFabric** и **ServiceFabricTestability** PowerShell. [Включите выполнение сценария PowerShell](service-fabric-get-started.md#enable-powershell-script-execution), а также [установите и запустите локальный кластер](service-fabric-get-started.md#install-and-start-a-local-cluster) для работы с примерами, приведенными в этой статье.

В примерах в этой статье используется [пример приложения **WordCount**](http://aka.ms/servicefabricsamples) (его можно найти в примерах "Приступая к работе"). Загрузите и постройте пример приложения.

Перед выполнением команд PowerShell, описанных в этой статье, необходимо подключиться к локальному кластеру Service Fabric с помощью команды [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## Задача: развернуть приложение Service Fabric

Собрав приложение и упаковав его тип, можно развернуть приложение в локальном кластере Service Fabric. Для начала упакуйте приложение WordCount в Visual Studio, щелкнув **WordCount** в обозревателе решений правой кнопкой мыши и выбрав параметр **Упаковать**. Сведения о манифестах службы и приложения и макета пакета см. в статье [Моделирование приложения в Service Fabric](service-fabric-application-model.md). Развертывание включает отправку пакета приложения, регистрацию типа приложения и создание экземпляра приложения. В этом разделе приводятся инструкции по развертыванию нового приложения в кластере.

### Шаг 1. Отправка пакета приложения
Отправка пакета приложения в хранилище образов означает, что он помещается в расположение, доступное внутренним компонентам Service Fabric. Пакет приложения содержит необходимый манифест приложения, манифесты служб и пакеты данных, конфигурации и кода для создания экземпляров приложения и служб. Команда [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) выполнит отправку пакета. Например:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Шаг 2. Регистрация пакета приложения
При регистрации пакета приложения его тип и версия, объявленные в манифесте приложения, становятся доступными для использования. Система считывает содержимое пакета, загруженного на предыдущем этапе, проверяет пакет (эквивалентно локальному выполнению команды [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx)), обрабатывает содержимое пакета и копирует обработанный пакет во внутреннее системное расположение. Выполните командлет [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx).

```powershell
Register-ServiceFabricApplicationType WordCount
```
Чтобы узнать, какие типы приложений зарегистрированы в кластере, выполните следующий командлет.

```powershell
Get-ServiceFabricApplicationType
```

### Шаг 3. Создание экземпляра приложения
Для создания экземпляра приложения можно использовать любую версию типа приложения, успешно зарегистрированную с помощью команды [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx). Имя приложения должно начинаться со схемы **fabric:** и быть уникальным для каждого экземпляра приложения. Имя и версия типа приложения объявляются в файле **ApplicationManifest.xml**. Если в манифесте приложения для конкретного его типа были определены используемые по умолчанию службы, они также будут созданы.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Команда [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) выводит список всех успешно созданных экземпляров приложения вместе с их общим состоянием. Команда [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) выводит список всех экземпляров службы, созданных в рамках конкретного экземпляра приложения. В список будут включены службы по умолчанию (если есть).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Задача: обновить приложение Service Fabric

Развернутое ранее приложение Service Fabric можно обновить. Данная задача обновляет приложение WordCount, развернутое при выполнении задачи по развертыванию приложения Service Fabric. Дополнительные сведения см. в разделе [Обновление приложения Service Fabric](service-fabric-application-upgrade.md).

### Шаг 1. Обновление приложения

Внесите изменения в код в службе WordCount.

Обновив код службы, увеличьте номер версии в файле **ServiceManifest.xml** (он находится в папке **PackageRoot** проекта WordCount). Найдите в манифесте элемент **CodePackage** и измените номер версии на 2.0.0. Соответствующие строки в файле ServiceManifest.xml должны выглядеть следующим образом:

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
	  ...
```

Теперь нужно обновить файл ApplicationManifest.xml (находится в проекте WordCount в составе решения WordCount). Обновите элемент **ServiceManifestRef**, указав версию 2.0.0.0 проекта **WordCountServicePkg**. Также измените параметр **ApplicationTypeVersion** с 1.0.0.0 на 2.0.0.0. Соответствующие строки в файле ApplicationManifest.xml должны принять следующий вид:

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

После внесения этих изменений сохраните файлы и выполните повторную сборку проекта WordCount. Теперь упакуйте обновленное приложение, щелкнув правой кнопкой мыши на проекте WordCount и выбрав **Упаковать**. При этом должен создаться пакет приложения, пригодный для развертывания. Теперь обновленное приложение готово к развертыванию.

### Шаг 2. Копирование и регистрация обновленного пакета приложения

Теперь приложение построено, упаковано и готово для обновления. Открыв окно PowerShell с правами администратора и введя команду [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), вы увидите, что развернуто приложение WordCount версии 1.0.0. Для примера WordCount пакет приложения находится в каталоге: *C:\\ServiceFabricSamples\\Services\\WordCount\\WordCount\\pkg\\Debug*.

Теперь скопируйте обновленный пакет приложения в хранилище образов (место, в которое Service Fabric сохраняет пакеты приложений). Параметр **ApplicationPackagePathInImageStore** информирует структуру служб о том, где находится пакет приложения. Следующая команда скопирует пакет приложения **WordCountV2** в хранилище образов:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

Далее необходимо зарегистрировать новую версию приложения в Service Fabric, выполнив для этого командлет [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

Если выполнение этой команды не завершается успешно, выполните сборку службы заново, как описано в шаге 1.

### Шаг 3. Запуск обновления
При обновлении приложения можно изменить различные параметры, время ожидания и критерий оценки работоспособности. Дополнительные сведения см. в статьях [Параметры обновления приложений](service-fabric-application-upgrade-parameters.md) и [Процесс обновления](service-fabric-application-upgrade.md). При изучении данного руководства сохраните критерий оценки работоспособности по умолчанию (а также рекомендуемые значения). После обновления все службы и экземпляры должны быть _работоспособны_. Однако необходимо увеличить значение атрибута **HealthCheckStableDuration** до 60 секунд (чтобы все службы были работоспособны хотя бы за 20 секунд до перехода обновления к другому домену обновления). Кроме того, укажите для атрибута **UpgradeDomainTimeout** значение 1200 секунд, а для атрибута **UpgradeTimeout** — 3000 секунд. И наконец, установите атрибут **UpgradeFailureAction** в значение **rollback**, чтобы Service Fabric выполняла откат приложения до предыдущей версии при возникновении любых неполадок во время обновления.

Теперь можно запустить обновление приложения, выполнив командлет [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Обратите внимание на то, что имя приложения совпадает с именем приложения развернутой ранее версии 1.0.0 (fabric:/WordCount). Структура служб использует это имя, чтобы распознать приложение, которое будет обновлено. Если у вас установлены слишком малые значения времени ожидания, может возникнуть сообщение об ошибке времени ожидания, в котором будет обозначена возникшая проблема. См. статью [Устранение неполадок при обновлениях приложений](service-fabric-application-upgrade-troubleshooting.md) или увеличьте время ожидания.

За обновлением приложения можно следить с помощью [обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md) или командлета [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Через несколько минут командлет [Get ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) должен показать, что все домены обновления обновлены (обновление завершено).

## Задача: протестировать приложение Service Fabric

Чтобы создавать высококачественные службы, разработчики должны иметь возможность вызывать сбои в ненадежных инфраструктурах, чтобы проверить стабильность работы своих служб. Платформа Service Fabric позволяет разработчикам вызывать сбои и тестировать службы при наличии последних с помощью сценариев хаотического тестирования и отработки отказа. Дополнительные сведения см. в статье [Обзор Testability](service-fabric-testability-overview.md).

### Шаг 1. Выполнение сценария хаотического тестирования
Сценарий хаотического тестирования создает сбои в масштабах всего кластера Service Fabric. Этот сценарий позволяет всего за несколько часов проработать ошибки, которые обычно наблюдаются в течение многих месяцев или лет. Сочетание сбоев с чередованием и сбоев с высокой частотой возникновения позволяет находить проблемы, которые в противном случае были бы упущены. В следующем примере сценарий хаотического тестирования запускается на 60 минут.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Шаг 2. Выполнение сценария отработки отказа
Сценарий тестовой отработки отказа задействует не весь кластер, а отдельный раздел, не затрагивая остальные службы. При выполнении этого сценария для проверки службы на фоне запущенной бизнес-логики повторяется последовательность моделируемых сбоев. Ошибка при проверке службы указывает на проблему, которую необходимо дополнительно изучить. При отработке отказа одновременно вызывается только один сбой, в то время как в процессе хаотического тестирования могут вызываться сразу несколько сбоев. Следующий пример запускает тест отработки отказа в течение 60 минут для службы fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

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
Если определенная версия типа приложения больше не требуется, отмените ее регистрацию с помощью командлета [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx). Отмена регистрации неиспользуемых типов поможет освободить пространство, занимаемое в хранилище образов пакетом приложения. Регистрацию типа приложения можно отменить в том случае, если на его основе не были созданы экземпляры приложений и нет ссылающихся на него незавершенных обновлений приложений.

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

<!---HONumber=AcomDC_0128_2016-->