# Обзор
## [Что такое Service Fabric?](service-fabric-overview.md)
## [Основные сведения о микрослужбах](service-fabric-overview-microservices.md)
## [Сценарии приложений](service-fabric-application-scenarios.md)
## [Архитектура](service-fabric-architecture.md)

# Начало работы
## Настройка среды разработки
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Создание своего первого приложения
### [C# в Windows](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java в Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [C# в Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Развертывание приложений в локальном кластере](service-fabric-get-started-with-a-local-cluster.md)

# Практическое руководство:
## Создание приложения
### Основы
#### [Модель программирования](service-fabric-choose-framework.md)
#### [Модель приложения](service-fabric-application-model.md)
#### [Взаимодействие служб](service-fabric-connect-and-communicate-with-services.md)
#### [Средства](service-fabric-manage-application-in-visual-studio.md)
#### [Отладка](service-fabric-debugging-your-application.md)
#### Мониторинг и диагностика
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Настройка политик безопасности для приложения](service-fabric-application-runas-security.md)
#### [Настройка приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md)

### Приложение Reliable Services
#### [Обзор](service-fabric-reliable-services-introduction.md)
#### Приступая к работе
##### [C# в Windows](service-fabric-reliable-services-quick-start.md)
##### [Java в Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Архитектура](service-fabric-reliable-services-platform-architecture.md)
#### [Надежные коллекции](service-fabric-reliable-services-reliable-collections.md)
#### [Использование надежных коллекций](service-fabric-work-with-reliable-collections.md)
#### [Настройка](service-fabric-reliable-services-configuration.md)
#### [Notifications](service-fabric-reliable-services-notifications.md)
#### [Архивация и восстановление](service-fabric-reliable-services-backup-restore.md)
#### [Обмен данными с помощью Reliable Services](service-fabric-reliable-services-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [Удаленное взаимодействие со службой](service-fabric-reliable-services-communication-remoting.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Обратный прокси-сервер](service-fabric-reverseproxy.md)
#### [Расширенное использование](service-fabric-reliable-services-advanced-usage.md)

### Приложение Reliable Actors
#### [Обзор](service-fabric-reliable-actors-introduction.md)
#### Начало работы
##### [C# в Windows](service-fabric-reliable-actors-get-started.md)
##### [Java в Linux](service-fabric-reliable-actors-get-started-java.md)
#### [Архитектура](service-fabric-reliable-actors-platform.md)
#### [Жизненный цикл и сборка мусора](service-fabric-reliable-actors-lifecycle.md)
#### [Полиморфизм](service-fabric-reliable-actors-polymorphism.md)
#### [Повторный вход](service-fabric-reliable-actors-reentrancy.md)
#### [Таймеры и напоминания](service-fabric-reliable-actors-timers-reminders.md)
#### [События](service-fabric-reliable-actors-events.md)
#### [Управление данными о состоянии](service-fabric-reliable-actors-state-management.md)
#### [Настройка поставщика состояния](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Сериализация типа](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### Гостевое исполняемое приложение
#### [Развертывание гостевого исполняемого файла](service-fabric-deploy-existing-app.md)
#### [Развертывание нескольких пользовательских приложений](service-fabric-deploy-multiple-apps.md)

### Приложение-контейнер
#### [Обзор](service-fabric-containers-overview.md)
#### [Развертывание контейнера Windows](service-fabric-deploy-container.md)
#### [Развертывание контейнера Docker](service-fabric-deploy-container-linux.md)

## Миграция из облачных служб
### [Сравнение облачных служб и Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Переход к использованию Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## Создание кластеров и управление ими

### Основы
#### [Обзор](service-fabric-deploy-anywhere.md)
#### [Описание кластера](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Планирование ресурсов](service-fabric-cluster-capacity.md)
#### [Визуализация кластера](service-fabric-visualizing-your-cluster.md)
#### [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md)
#### [Безопасность](service-fabric-cluster-security.md)
#### [Аварийное восстановление](service-fabric-disaster-recovery.md)

### Кластеры в Azure
#### Создание кластера в Azure
##### [Портал Azure](service-fabric-cluster-creation-via-portal.md)
##### [Диспетчер ресурсов Azure](service-fabric-cluster-creation-via-arm.md)
#### [Виды узлов и масштабируемые наборы ВМ](service-fabric-cluster-nodetypes.md)
#### [Масштабирование кластера](service-fabric-cluster-scale-up-down.md)
#### [Обновление кластера](service-fabric-cluster-upgrade.md)
#### [Удаление кластера](service-fabric-cluster-delete.md)
#### [Контроль доступа](service-fabric-cluster-security-roles.md)
#### [Настройка кластера](service-fabric-cluster-fabric-settings.md)
#### [Пробное бесплатное использование кластера Party Cluster](http://aka.ms/tryservicefabric)

### Изолированные кластеры
#### [Создание изолированного кластера](service-fabric-cluster-creation-for-windows-server.md)
#### [Масштабирование кластера](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Обновление кластера](service-fabric-cluster-upgrade-windows-server.md)
#### [Обеспечение безопасности кластера](service-fabric-windows-cluster-x509-security.md)
#### [Контроль доступа](service-fabric-cluster-security-roles.md)
#### [Настройка кластера](service-fabric-cluster-manifest.md)

## Ресурсы кластера: управление и оркестрация
### [Обзор диспетчера кластерных ресурсов](service-fabric-cluster-resource-manager-introduction.md)
### [Архитектура диспетчера кластерных ресурсов](service-fabric-cluster-resource-manager-architecture.md)
### [Описание кластера](service-fabric-cluster-resource-manager-cluster-description.md)
### [Обзор групп приложений](service-fabric-cluster-resource-manager-application-groups.md)
### [Настройка параметров диспетчера кластерных ресурсов](service-fabric-cluster-resource-manager-configure-services.md)
### [Метрики потребления ресурсов](service-fabric-cluster-resource-manager-metrics.md)
### [Использование сходства служб](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Политики размещения служб](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Управление кластером](service-fabric-cluster-resource-manager-management-integration.md)
### [Дефрагментация кластера](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Балансировка кластера](service-fabric-cluster-resource-manager-balancing.md)
### [Регулирование](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Перемещение служб](service-fabric-cluster-resource-manager-movement-cost.md)

## Управление жизненным циклом приложения
### [Обзор](service-fabric-application-lifecycle.md)
### [Настройка непрерывной интеграции](service-fabric-set-up-continuous-integration.md)
### Развертывание или удаление приложений
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [Обзор обновления приложения](service-fabric-application-upgrade.md)
### [Настройка обновления приложения](service-fabric-visualstudio-configure-upgrade.md)
### [Параметры обновления приложений](service-fabric-application-upgrade-parameters.md)
### Обновление приложения
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Устранение неполадок при обновлениях приложений](service-fabric-application-upgrade-troubleshooting.md)
### [Сериализация данных при обновлении приложения](service-fabric-application-upgrade-data-serialization.md)
### [Обновление приложения: более сложные темы](service-fabric-application-upgrade-advanced.md)
### [Пример жизненного цикла приложения на основе REST](service-fabric-rest-based-application-lifecycle-sample.md)

## Проверка работоспособности приложения и кластера
### [Отслеживание работоспособности Service Fabric](service-fabric-health-introduction.md)
### [Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Добавление настраиваемых отчетов о работоспособности](service-fabric-report-health.md)
### [Устранение неполадок с помощью отчетов о работоспособности системы](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Просмотр отчетов о работоспособности](service-fabric-view-entities-aggregated-health.md)

## Мониторинг и диагностика
### Мониторинг и диагностика состояния служб в локальной среде разработки
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Журналы системы диагностики Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Трассировка приложения Service Fabric](service-fabric-diagnostic-how-to-use-elasticsearch.md)
### [Диагностика в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Диагностика в Reliable Services с отслеживанием состояния](service-fabric-reliable-services-diagnostics.md)
### [Устранение неполадок локального кластера](service-fabric-troubleshoot-local-cluster-setup.md)
### [Устранение распространенных неполадок](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

## Масштабирование приложений
### [Секционирование Reliable Services](service-fabric-concepts-partitioning.md)
### [Доступность служб](service-fabric-availability-services.md)
### [Масштабирование приложений](service-fabric-concepts-scalability.md)
### [Планирование загрузки приложений](service-fabric-capacity-planning.md)

## Тестирование приложений и служб
### [Обзор анализа ошибок](service-fabric-testability-overview.md)
### [Тестирование связи между службами](service-fabric-testability-scenarios-service-communication.md)
### Моделирование сбоев
#### [Использование контролируемого хаоса](service-fabric-controlled-chaos.md)
#### [Использование тестовых действий](service-fabric-testability-actions.md)
#### [При выполнении рабочих нагрузок](service-fabric-testability-workload-tests.md)
#### [Путем вызова потери данных](service-fabric-use-data-loss-api.md)
#### [Использование тестовых сценариев](service-fabric-testability-scenarios.md)
### [Нагрузочный тест приложения](service-fabric-vso-load-test.md)

# Справочные материалы
## [Терминология](service-fabric-technical-overview.md)
## [Reliable Actors](https://go.microsoft.com/fwlink/p/?linkid=833398)
## [WCF для Reliable Actors](https://go.microsoft.com/fwlink/p/?linkid=833401)
## [Reliable Services](https://go.microsoft.com/fwlink/p/?linkid=833402)
## [WCF для Reliable Services](https://go.microsoft.com/fwlink/p/?linkid=833403)
## [Данные](https://go.microsoft.com/fwlink/p/?linkid=833404)
## [Интерфейсы данных](https://go.microsoft.com/fwlink/p/?linkid=833406)
## [Система](https://go.microsoft.com/fwlink/p/?linkid=833407)
## [PowerShell](https://go.microsoft.com/fwlink/p/?linkid=833408)
## [ИНТЕРФЕЙС REST API](https://go.microsoft.com/fwlink/p/?LinkID=532910)
## [API Java](https://go.microsoft.com/fwlink/p/?linkid=833410)
## [Пример кода](http://aka.ms/servicefabricsamples)

# Ресурсы
## [Схема обучения](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=service-fabric&updatetype=&platform=)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)


<!--HONumber=Nov16_HO2-->


