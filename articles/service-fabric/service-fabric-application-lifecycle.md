---
title: Жизненный цикл приложений в Service Fabric | Документация Майкрософт
description: Описывает разработку, развертывание, тестирование, обновление, обслуживание и удаление приложений Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: ryanwi
ms.openlocfilehash: e7dddfca3640615cb851fb6dce9eaa80260ccbf6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212567"
---
# <a name="service-fabric-application-lifecycle"></a>Жизненный цикл приложения Service Fabric
Как и в случае с другими платформами, приложение в Azure Service Fabric обычно проходит следующие фазы: проектирование, разработка, тестирование, развертывание, обновление, техническое обслуживание и удаление. Service Fabric предоставляет первоклассную поддержку полного жизненного цикла приложений в облаке: от разработки, развертывания, ежедневного управления и технического обслуживания до вывода приложения из эксплуатации. Модель службы использует несколько различных ролей для независимого участия в жизненном цикле приложения. В этой статье представлен обзор API и того, как они используются различными ролями на протяжении всех фаз жизненного цикла приложения в Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

В следующем видео от Академии Microsoft Virtual Academy также описывается процесс управления жизненным циклом приложения: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Роли моделей служб
Роли моделей служб:

* **Разработчик службы.** Разрабатывает модульные и универсальные службы, для которых можно переопределить цель и которые можно использовать в различных приложениях одного и того же или разных типов. Например, служба очередей может использоваться для создания приложения для обработки обращений (служба поддержки) или приложения для электронной коммерции (корзины).
* **Разработчик приложения.** Создает приложения путем интеграции коллекции служб для обеспечения соответствия определенным конкретным требованиям или сценариям. Например, на веб-сайте электронной коммерции могут интегрироваться интерфейсные службы JSON без отслеживания состояния службы, аукцион с отслеживанием состояния службы и служба очереди с отслеживанием состояния службы для создания аукционного приложения.
* **Администратор приложения.** Принимает решения о конфигурации приложения (установка параметров шаблона конфигурации), развертывании (сопоставление с доступными ресурсами) и обеспечении качества обслуживания. Например, администратор приложения принимает решение о языковых настройках приложения, используемых в зависимости от региона (например, английский для США или японский для Японии). Другое развернутое приложение может иметь другие настройки.
* **Оператор.** Развертывает приложения с учетом конфигурации приложения и требований, указанных администратором приложения. Например, оператор выделяет и развертывает приложение и обеспечивает его работу в Azure. Операторы должны отслеживать работоспособность и производительность приложений и поддерживать соответствующую физическую инфраструктуру при необходимости.

## <a name="develop"></a>Разработка
1. *Разработчик службы* разрабатывает различные типы служб, используя модель программирования [Reliable Actors](service-fabric-reliable-actors-introduction.md) или [Reliable Services](service-fabric-reliable-services-introduction.md).
2. *Разработчик службы* декларативно описывает типы разработанных служб в файле манифеста служб, состоящего из одного или нескольких блоков кода, конфигурации и пакетов данных.
3. *Разработчик приложений* затем создает приложения, используя для этого службы различных типов.
4. *Разработчик приложений* декларативно описывает тип приложения в манифесте приложения путем ссылок на манифесты составляющих его служб и применяет переопределение и назначение параметров различных конфигураций и настроек развертывания служб, из которых состоит приложение.

См. статьи [Приступая к работе с Reliable Actors](service-fabric-reliable-actors-get-started.md) и [Начало работы со службами Reliable Services в Service Fabric](service-fabric-reliable-services-quick-start.md), чтобы ознакомиться с примерами.

## <a name="deploy"></a>Развертывание
1. *Администратор приложения* изменяет приложение определенного типа для конкретного приложения, которое будет развернуто в кластере Service Fabric путем указания соответствующих параметров элемента **ApplicationType** в манифесте приложения.
2. *Оператор* загружает пакет приложения в хранилище образов кластера с помощью [метода **CopyApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) или [командлета **Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Пакет приложения содержит манифест приложения и коллекцию пакетов служб. Структура служб выполняет развертывание приложений из пакета приложений, размещенного в хранилище образов, который может представлять собой магазин больших двоичных объектов Azure или системную службу Service Fabric.
3. *Оператор* затем выделяет тип приложения в целевом кластере из загруженного пакета приложения с помощью [метода **ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) или [операции REST **Provision an Application**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. После подготовки приложения *оператор* запускает приложение с параметрами, предоставленными *администратором приложения* с помощью [метода **CreateApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [командлета **New-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication) или [операции REST **Create Application**](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. После того как приложение будет развернуто, *оператор* использует [метод **CreateServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [командлет **New-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice) или [операцию REST **Create Service**](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) для создания экземпляров службы для приложения на основании доступных типов службы.
6. Теперь приложение работает в кластере Service Fabric.

См. статью [Развертывание и удаление приложений с помощью PowerShell](service-fabric-deploy-remove-applications.md), чтобы ознакомиться с примерами.

## <a name="test"></a>Тест
1. После развертывания в локальном кластере разработки или в тестовом кластере *разработчик службы* запускает встроенный сценарий проверки переключения на резервный ресурс с помощью классов [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) и [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) или [командлета **Invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). Сценарий тестирования отказа пропускает указанную службу через важные преобразования и отказы, чтобы гарантировать, что она остается доступной и продолжает работать.
2. Затем *разработчик службы* запускает встроенный хаотический сценарий тестирования с помощью классов [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) и [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) или [командлета **Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). Хаотический сценарий тестирования в случайном порядке вызывает множественные ошибки на уровне узла, пакета кода и реплики в кластере.
3. *Разработчик службы* [тестирует обмен данными между службами](service-fabric-testability-scenarios-service-communication.md) , создавая сценарии тестирования для перемещения первичных реплик в кластере.

Дополнительные сведения см. в статье [Общие сведения о службе анализа сбоев](service-fabric-testability-overview.md).

## <a name="upgrade"></a>Обновление
1. *Разработчик службы* обновляет составляющие службы экземпляра приложения или устраняет ошибки кода и предоставляет новую версию манифеста служб.
2. *Разработчик приложения* переопределяет и параметризует настройки конфигурации и развертывания и предоставляет новую версию манифеста приложения. Разработчик приложения затем включает новые версии манифестов служб в приложение и собирает новую версию приложения этого же типа в обновленном пакете приложения.
3. *Администратор приложения* включает новую версию приложения этого же типа в целевое приложение путем обновления соответствующих параметров.
4. *Оператор* загружает обновленный пакет приложения в хранилище образов кластера с помощью [метода **CopyApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) или [командлета **Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). Пакет приложения содержит манифест приложения и коллекцию пакетов служб.
5. *Оператор* предоставляет новую версию приложения для целевого кластера с помощью [метода **ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) или [операции REST **Provision an Application**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. *Оператор* обновляет целевое приложение до новой версии, используя [метод **UpgradeApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), [командлет **Start-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade) или [операцию **REST Upgrade an Application**](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. *Оператор* проверяет ход обновления с помощью [метода **GetApplicationUpgradeProgressAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Get-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) или [операции REST **Get Application Upgrade Progress**](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. При необходимости *оператор* изменяет и повторно применяет параметры текущего обновления приложения с помощью [метода **UpdateApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Update-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade) или [операции **REST Update Application Upgrade**](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. При необходимости *оператор* применяет откат текущего обновления приложения с помощью [метода **RollbackApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Start-ServiceFabricApplicationRollback**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback) или [операции **RESTRollback Application Upgrade**](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Структура службы обновляет целевое приложение, запущенное в кластере приложения без потери доступности служб, которые входят в его состав.

См. [руководство по обновлению приложений Service Fabric с помощью Visual Studio](service-fabric-application-upgrade-tutorial.md), чтобы ознакомиться с примерами.

## <a name="maintain"></a>Техническое обслуживание
1. Для обновлений и исправлений операционной системы структура служб взаимодействует с инфраструктурой Azure таким образом, чтобы обеспечивать гарантированную доступность всех приложений в кластере.
2. Для обновлений и исправлений в платформе Service Fabric обновление самой Service Fabric выполняется без потери доступности любых приложений, запущенных в кластере.
3. *Администратор приложения* утверждает добавление или удаление узлов в кластере после выполнения анализа архивных данных об использовании мощностей и прогнозируемой потребности в мощностях в будущем.
4. *Оператор* добавляет и удаляет узлы, указанные *администратором приложения*.
5. При добавлении новых или удалении существующих узлов из кластера структура службы автоматически балансирует нагрузку запущенных приложений на всех узлах в кластере, чтобы достичь оптимальной производительности.

## <a name="remove"></a>Удалить
1. *Оператор* может удалить определенный экземпляр запущенной службы в кластере без удаления всего приложения с помощью [метода **DeleteServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Remove-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice) или [операции REST **Delete Service**](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. *Оператор* может также удалить экземпляр приложения и все его службы с помощью [метода **DeleteApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Remove-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication) или [операции **REST Delete Application**](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. После того как приложения и службы будут остановлены, *оператор* может отменить выделение мощностей для этого типа приложений с помощью [метода **UnprovisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), [командлета **Unregister-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) или [операции **REST Unprovision an Application**](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). При отмене подготовки мощностей для определенного типа приложения пакет приложения не удаляется из хранилища образов; необходимо удалить его вручную. Пакет приложения необходимо удалить вручную.
4. *Оператор* удаляет пакет приложений из ImageStore с помощью [метода **RemoveApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) или [командлета **Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

См. статью [Развертывание и удаление приложений с помощью PowerShell](service-fabric-deploy-remove-applications.md), чтобы ознакомиться с примерами.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о разработке и тестировании приложений Service Fabric и служб, а также об управлении ими см. в следующих разделах.

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Надежные службы](service-fabric-reliable-services-introduction.md)
* [Развертывание приложения](service-fabric-deploy-remove-applications.md)
* [Обновление приложения](service-fabric-application-upgrade.md)
* [Обзор Testability](service-fabric-testability-overview.md)
