---
title: Обновление приложения Service Fabric с помощью PowerShell | Документация Майкрософт
description: В этой статье рассматривается процесс развертывания приложения Service Fabric, изменения кода и развертывания обновления с помощью PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 44f4dc3a9c876e383a6e4df8ef5f467f2b93eaa9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205490"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Обновление приложения Service Fabric с помощью PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Наиболее часто используемый и рекомендуемый подход к выполнению обновлений состоит в отслеживаемом последовательном обновлении.  Платформа Azure Service Fabric осуществляет мониторинг работоспособности обновляемого приложения на основе набора политик работоспособности. После обновления домена обновления Service Fabric оценивает работоспособность приложения, после чего переходит к следующему домену обновления или регистрирует сбой в соответствии с политиками работоспособности.

Отслеживаемое обновление приложения может выполняться с использованием управляемых или собственных API, PowerShell или REST. Инструкции по обновлению с помощью Visual Studio см. в статье [Руководство по обновлению приложений Service Fabric с помощью Visual Studio](service-fabric-application-upgrade-tutorial.md).

Service Fabric выполняет отслеживаемое последовательное обновление, позволяя администратору приложений настраивать политики оценки работоспособности, которые Service Fabric будет использовать для определения работоспособности приложения. Кроме того, администратор может настраивать действия, выполняемые при сбое оценки работоспособности (например, автоматический откат приложения). В этом разделе представлено пошаговое руководство по отслеживаемому обновлению одного из образцов пакета SDK с использованием PowerShell. В следующем видео от Академии Microsoft Virtual Academy также представлено пошаговое обновление приложения: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=OrHJH66yC_6406218965">
<img src="./media/service-fabric-application-upgrade-tutorial-powershell/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Шаг 1. Построение и развертывание образца визуальных объектов
Создайте и опубликуйте приложение. Для этого щелкните правой кнопкой мыши проект приложения **VisualObjectsApplication** и выберите **Опубликовать**.  Дополнительные сведения см. в [руководстве по обновлению приложений Service Fabric](service-fabric-application-upgrade-tutorial.md).  Или разверните приложение с помощью PowerShell.

> [!NOTE]
> Чтобы в PowerShell можно было использовать хотя бы одну из команд Service Fabric, необходимо сначала подключиться к соответствующему кластеру с помощью командлета `Connect-ServiceFabricCluster`. Предполагается, что кластер уже настроен на локальном компьютере. См. статью, посвященную [настройке среды разработки Service Fabric](service-fabric-get-started.md).
> 
> 

Создав проект в Visual Studio, вы можете использовать команду PowerShell [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) , чтобы скопировать пакет приложения в ImageStore. Если вы хотите проверить пакет приложения локально, используйте командлет [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage). Следующий шаг — регистрация приложения в среде выполнения Service Fabric с помощью командлета [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype). Следующий шаг — запуск экземпляра приложения с помощью командлета [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps).  Эти три шага аналогичны действиям при использовании меню **Развертывание** в Visual Studio.  После завершения подготовки следует удалить скопированный пакет приложения из хранилища образов, чтобы сократить потребление ресурсов.  Если тип приложения больше не требуется, необходимо отменить его регистрацию. См. дополнительные сведения о [развертывании и удалении приложений с помощью PowerShell](service-fabric-application-upgrade-tutorial-powershell.md).

Теперь можно использовать [обозреватель структуры служб для просмотра кластера и приложения](service-fabric-visualizing-your-cluster.md). Приложение имеет веб-службу, к которой можно перейти, введя в адресной строке браузера Internet Explorer следующее: [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects)  Вы сможете увидеть несколько плавающих визуальных объектов, перемещающихся по экрану.  Кроме того, можно использовать командлет [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) , чтобы проверить состояние приложения.

## <a name="step-2-update-the-visual-objects-sample"></a>Шаг 2. Обновление образца визуальных объектов
Вы можете заметить, что в версии, развернутой на шаге 1, визуальные объекты не вращаются. Давайте обновим это приложение до версии, в которой визуальные объекты будут вращаться.

Выберите проект VisualObjects.ActorService в решении VisualObjects и откройте файл StatefulVisualObjectActor.cs. В этом файле перейдите к методу `MoveObject`, а затем закомментируйте `this.State.Move()` и раскомментируйте `this.State.Move(true)`. Это изменение приведет к вращению объектов после обновления службы.

Вам также необходимо обновить файл *ServiceManifest.xml* (в разделе PackageRoot) проекта **VisualObjects.ActorService**. Обновите *CodePackage* и версию службы до версии 2.0 и соответствующие строки в файле *ServiceManifest.xml*.
Чтобы изменить файл манифеста, в Visual Studio можно щелкнуть решение правой кнопкой мыши и выбрать пункт меню *Изменить файлы манифеста* .

После внесения изменений манифест должен выглядеть следующим образом (измененные части выделены полужирным шрифтом):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Теперь файл *ApplicationManifest.xml* (находится в проекте **VisualObjects** в решении **VisualObjects**) обновлен для использования версии 2.0 проекта **VisualObjects.ActorService**. Кроме того, приложение версии 1.0.0.0 обновлено до версии 2.0.0.0. Файл *ApplicationManifest.xml* должен выглядеть как следующий фрагмент кода.

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Теперь выполните сборку проекта. Для этого в Visual Studio выберите проект **ActorService** и щелкните правой кнопкой мыши пункт **Собрать**. Если выбрать **Перестроить все**, то потребуется обновить версии для всех проектов, так как код изменится. Теперь создайте пакет обновленного приложения. Для этого щелкните правой кнопкой проект ***VisualObjectsApplication*** и выберите в меню Service Fabric пункт **Пакет**. При этом должен создаться пакет приложения, пригодный для развертывания.  Обновленное приложение готово к развертыванию.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Шаг 3. Определение политик работоспособности и параметров обновления
Ознакомьтесь с [параметрами обновления приложений](service-fabric-application-upgrade-parameters.md) и [процессом обновления](service-fabric-application-upgrade.md), чтобы лучше понимать суть разных применяемых параметров обновления, значений времени ожидания и критериев работоспособности. В этом пошаговом руководстве для критерия оценки работоспособности службы заданы значения по умолчанию (рекомендуемые значения). Это значит, что все службы и экземпляры после обновления должны быть работоспособными (иметь состояние *Исправно*).  

Тем не менее давайте увеличим значение атрибута *HealthCheckStableDuration* до 60 секунд (чтобы все службы были работоспособны хотя бы 20 секунд, до того как обновление будет запущено в другом домене обновления).  Давайте также установим значение атрибута *UpgradeDomainTimeout* в значение "1200 секунд", а значение *UpgradeTimeout* — в значение "3000 секунд".

Наконец, давайте также настроим *UpgradeFailureAction* , чтобы выполнять откат. Этот параметр указывает Service Fabric выполнять откат приложения до предыдущей версии при возникновении проблем во время обновления. Таким образом при запуске обновления (на шаге 4) заданы следующие параметры:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Шаг 4. Подготовка приложения к обновлению
Теперь приложение создано и готово для обновления. Если открыть окно PowerShell с правами администратора и ввести команду [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), в нем отобразится информация о том, что вы развернули приложение типа **VisualObjects** версии 1.0.0.0.  

Пакет приложения хранится по следующему относительному пути в зависимости от того, куда был извлечен SDK Service Fabric: *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Следует найти папку Package в этом каталоге (где хранится пакет приложения). С помощью временных меток проверьте, что это самая последняя версия (возможно, вам нужно будет соответствующим образом изменить пути).

Теперь давайте скопируем обновленный пакет приложения в ImageStore структуры службы (место, где структурой служб сохраняются пакеты приложения). Параметр *ApplicationPackagePathInImageStore* информирует структуру служб о том, где находится пакет приложения. Мы поместили обновленное приложение в папку VisualObjects\_V2 с помощью следующей команды (возможно, вам снова нужно будет изменить пути).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Далее необходимо зарегистрировать приложение в Service Fabric. Для этого выполните команду [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps):

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Если предыдущая команда не выполняется, скорее всего, вам нужно перестроить все службы. Как упомянуто на шаге 2, возможно, потребуется обновить версию объекта WebService.

Рекомендуется удалить пакет приложения после успешной регистрации приложения.  Удаление пакетов приложений из хранилища образов освобождает системные ресурсы.  Если хранить неиспользуемые пакеты, они занимают место на диске и создают проблемы производительности приложения.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Шаг 5. Начало обновления приложения
Теперь вы можете запустить обновление приложения с помощью команды [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Имя приложения соответствует имени, указанному в файле *ApplicationManifest.xml*. Структура служб использует это имя, чтобы распознать приложение, которое будет обновлено. Если для времени ожидания вы установили слишком малые значения, может возникнуть сообщение об ошибке, информирующее о проблеме. См. раздел по устранению неполадок или увеличьте значения времени ожидания.

Вы можете отслеживать ход обновления с помощью Service Fabric Explorer или команды PowerShell [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps): 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Через несколько минут в состоянии, полученном с помощью предыдущей команды PowerShell, отобразится информация о завершении обновления всех доменов обновления. Все визуальные объекты в окне браузера должны вращаться.

В качестве упражнения вы можете выполнить обновление с версии 2 до версии 3 или с версии 2 до версии 1. Переход с версии 2 на версию 1 также считается обновлением. Попробуйте изменить значения времени ожидания и параметры политики работоспособности в качестве тренировки. При развертывании в кластере Azure необходимо правильно настроить параметры. Рекомендуется использовать консервативный подход при настройке значений времени ожидания.

## <a name="next-steps"></a>Дополнительная информация
[Руководство по обновлению приложений Service Fabric с помощью Visual Studio](service-fabric-application-upgrade-tutorial.md) поможет вам выполнить поэтапное обновление приложения с помощью Visual Studio.

Управление обновлениями приложения осуществляется с помощью [параметров обновления](service-fabric-application-upgrade-parameters.md).

Узнайте, как использовать [сериализацию данных](service-fabric-application-upgrade-data-serialization.md), чтобы обеспечить совместимость обновлений приложения.

[Дополнительные разделы](service-fabric-application-upgrade-advanced.md)содержат сведения о работе с расширенными функциями при обновлении приложения.

Сведения об устранении распространенных проблем при обновлении приложений см. в статье [Устранение неполадок обновления приложения](service-fabric-application-upgrade-troubleshooting.md).

