<properties
   pageTitle="Жизненный цикл приложений в Service Fabric | Microsoft Azure"
   description="Описывает разработку, развертывание, тестирование, обновление, обслуживание и удаление приложений Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# Жизненный цикл приложения Service Fabric
Как и в случае с другими платформами, приложение в Azure Service Fabric обычно проходит следующие фазы: проектирование, разработка, тестирование, развертывание, обновление, техническое обслуживание и удаление. Service Fabric предоставляет первоклассную поддержку полного жизненного цикла приложений в облаке: от разработки, развертывания, ежедневного управления и технического обслуживания до вывода приложения из эксплуатации. Модель службы использует несколько различных ролей для независимого участия в жизненном цикле приложения. В этой статье представлен обзор API и того, как они используются различными ролями на протяжении всех фаз жизненного цикла приложения в Service Fabric.

## Роли моделей служб
Роли моделей служб:

- **Разработчик службы**. Разрабатывает модульные и универсальные службы, для которых можно переопределить цель и которые можно использовать в различных приложениях одного и того же или разных типов. Например, служба очередей может использоваться для создания приложения для обработки обращений (служба поддержки) или приложения для электронной коммерции (корзины).

- **Разработчик приложения**. Создает приложения путем интеграции коллекции служб для обеспечения соответствия определенным конкретным требованиям или сценариям. Например, на веб-сайте электронной коммерции могут интегрироваться интерфейсные службы JSON без отслеживания состояния службы, аукцион с отслеживанием состояния службы и служба очереди с отслеживанием состояния службы для создания аукционного приложения.

- **Администратор приложения**. Принимает решения о конфигурации приложения (установка параметров шаблона конфигурации), развертывании (сопоставление с доступными ресурсами) и обеспечении качества обслуживания. Например, администратор приложения принимает решение о языковых настройках приложения, используемых в зависимости от региона (например, английский для США или японский для Японии). Другое развернутое приложение может иметь другие настройки.

- **Оператор**. Развертывает приложения с учетом конфигурации приложения и требований, указанных администратором приложения. Например, оператор выделяет и развертывает приложение и обеспечивает его работу в Azure. Операторы должны отслеживать работоспособность и производительность приложений и поддерживать соответствующую физическую инфраструктуру при необходимости.


## Разработка
1. *Разработчик службы* разрабатывает различные типы служб, используя модель программирования [Reliable Actors](service-fabric-reliable-actors-introduction.md) или [Reliable Services](service-fabric-reliable-services-introduction.md).
2. *Разработчик службы* декларативно описывает типы разработанных служб в файле манифеста служб, состоящего из одного или нескольких блоков кода, конфигурации и пакетов данных.
3. *Разработчик приложений* затем создает приложения, используя для этого службы различных типов.
4. *Разработчик приложений* декларативно описывает тип приложения в манифесте приложения путем ссылок на манифесты составляющих его служб и применяет переопределение и назначение параметров различных конфигураций и настроек развертывания служб, из которых состоит приложение.

См. [Начало работы с Reliable Actors](service-fabric-reliable-actors-get-started.md) и [Начало работы с Reliable Services](service-fabric-reliable-services-quick-start.md), чтобы ознакомиться с примерами.

## Развернуть
1. *Администратор приложения* изменяет приложение определенного типа для конкретного приложения, которое будет развернуто в кластере Service Fabric путем указания соответствующих параметров элемента **ApplicationType** в манифесте приложения.

2. *Оператор* загружает пакет приложения в хранилище образов кластера при помощи [метода **CopyApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) или [командлета **Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). Пакет приложения содержит манифест приложения и коллекцию пакетов служб. Структура служб выполняет развертывание приложений из пакета приложений, размещенного в хранилище образов, который может представлять собой магазин больших двоичных объектов Azure или системную службу Service Fabric.

3. *Оператор* затем выделяет тип приложения в целевом кластере из загруженного пакета приложения при помощи [метода **ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), [командлета **Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) или [операции REST **Provision an Application**](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. После подготовки приложения *оператор* запускает приложение с параметрами, предоставленными *администратором приложения* при помощи [метода **CreateApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), [командлета **New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) или [операции REST **Create Application**](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. После того как приложение будет развернуто, *оператор* использует [метод **CreateServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), [командлет **New-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125874.aspx) или [операцию REST **Create Service**](https://msdn.microsoft.com/library/azure/dn707657.aspx) для создания новых экземпляров службы для приложения на основании доступных типов службы.

6. Теперь приложение работает в кластере Service Fabric.

См. раздел [Развертывание приложения](service-fabric-deploy-remove-applications.md), чтобы ознакомиться с примерами.

## Тест
1. После развертывания в локальном кластере разработки или в тестовом кластере *разработчик службы* запускает встроенный сценарий проверки переключения на резервный ресурс при помощи классов [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) и [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) или [командлета **Invoke-ServiceFabricFailoverTestScenario**](https://msdn.microsoft.com/library/azure/mt644783.aspx). Сценарий тестирования отказа пропускает указанную службу через важные преобразования и отказы, чтобы гарантировать, что она остается доступной и продолжает работать.

2. Затем *разработчик службы* запускает встроенный хаотический сценарий тестирования при помощи классов [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) и [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) или [командлета **Invoke-ServiceFabricChaosTestScenario**](https://msdn.microsoft.com/library/azure/mt644774.aspx). Хаотический сценарий тестирования в случайном порядке вызывает множественные ошибки на уровне узла, пакета кода и реплики в кластере.

3. *Разработчик службы* [тестирует обмен данными между службами](service-fabric-testability-scenarios-service-communication.md), создавая сценарии тестирования для перемещения первичных реплик в кластере.

Дополнительные сведения см. в разделе [Introduction to the Fault Analysis Service (Общие сведения о службе анализа сбоев)](service-fabric-testability-overview.md).

## Обновление
1. *Разработчик службы* обновляет составляющие службы экземпляра приложения или устраняет ошибки кода и предоставляет новую версию манифеста служб.

2. *Разработчик приложения* переопределяет и параметризует настройки конфигурации и развертывания и предоставляет новую версию манифеста приложения. Разработчик приложения затем включает новые версии манифестов служб в приложение и собирает новую версию приложения этого же типа в обновленном пакете приложения.

3. *Администратор приложения* включает новую версию приложения этого же типа в целевое приложение путем обновления соответствующих параметров.

5. *Оператор* загружает обновленный пакет приложения в хранилище образов кластера при помощи [метода **CopyApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) или [командлета **Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). Пакет приложения содержит манифест приложения и коллекцию пакетов служб.

6. *Оператор* предоставляет новую версию приложения для целевого кластера при помощи [метода **ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), [командлета **Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) или [операции REST P**rovision an Application**](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. *Оператор* обновляет целевое приложение до новой версии, используя [метод **UpgradeApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), [командлет **Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) или [операцию REST **Upgrade an Application**](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. *Оператор* проверяет ход обновления при помощи [метода **GetApplicationUpgradeProgressAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), [командлета **Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) или [операции REST **Get Application Upgrade Progress**](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. При необходимости *оператор* изменяет и повторно применяет параметры текущего обновления приложения при помощи [метода **UpdateApplicationUpgradeAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), [командлета **Update-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt126030.aspx) или [операции REST **Update Application Upgrade**](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. При необходимости *оператор* применяет откат текущего обновления приложения при помощи [метода **RollbackApplicationUpgradeAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), [командлета **Start-ServiceFabricApplicationRollback**](https://msdn.microsoft.com/library/azure/mt125833.aspx) или [операции REST** Rollback Application Upgrade**](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Структура службы обновляет целевое приложение, запущенное в кластере приложения без потери доступности служб, которые входят в его состав.

См. [Учебник по обновлению приложений](service-fabric-application-upgrade-tutorial.md), чтобы ознакомиться с примерами.

## Техническое обслуживание
1. Для обновлений и исправлений операционной системы структура служб взаимодействует с инфраструктурой Azure таким образом, чтобы обеспечивать гарантированную доступность всех приложений в кластере.

2. Для обновлений и исправлений в платформе Service Fabric обновление самой Service Fabric выполняется без потери доступности любых приложений, запущенных в кластере.

3. *Администратор приложения* утверждает добавление или удаление узлов в кластере после выполнения анализа архивных данных об использовании мощностей и прогнозируемой потребности в мощностях в будущем.

4. *Оператор* добавляет и удаляет узлы, указанные *администратором приложения*.

5. При добавлении новых или удалении существующих узлов из кластера структура службы автоматически балансирует нагрузку запущенных приложений на всех узлах в кластере, чтобы достичь оптимальной производительности.

## Удалить
1. *Оператор* может удалить определенный экземпляр запущенной службы в кластере без удаления всего приложения при помощи [метода **DeleteServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), [командлета **Remove-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt126033.aspx) или [операции REST **Delete Service**](https://msdn.microsoft.com/library/azure/dn707687.aspx).

2. *Оператор* может также удалить экземпляр приложения и все его службы при помощи [метода **DeleteApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), [командлета **Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) или [операции REST **Delete Application**](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. После того как приложения и службы будут остановлены, *оператор* может отменить выделение мощностей для этого типа приложений при помощи [метода **UnprovisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), [командлета **Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) или [операции REST **Unprovision an Application**](https://msdn.microsoft.com/library/azure/dn707671.aspx). При отмене подготовки мощностей для определенного типа приложения пакет приложения не удаляется из хранилища образов; необходимо удалить его вручную. Пакет приложения необходимо удалить вручную.

4. *Оператор* удаляет пакет приложений из ImageStore при помощи [метода **RemoveApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) или [командлета **Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

См. раздел [Развертывание приложения](service-fabric-deploy-remove-applications.md), чтобы ознакомиться с примерами.

## Дальнейшие действия

Дополнительные сведения о разработке и тестировании приложений Service Fabric и служб, а также об управлении ими см. в следующих разделах.

- [Надежные субъекты](service-fabric-reliable-actors-introduction.md)
- [Надежные службы](service-fabric-reliable-services-introduction.md)
- [Развертывание приложения](service-fabric-deploy-remove-applications.md)
- [Обновление приложения](service-fabric-application-upgrade.md)
- [Обзор Testability](service-fabric-testability-overview.md)
- [Пример жизненного цикла приложения на основе REST](service-fabric-rest-based-application-lifecycle-sample.md)

<!---HONumber=AcomDC_0831_2016-->