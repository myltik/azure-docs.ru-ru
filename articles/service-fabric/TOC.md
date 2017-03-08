# Обзор
## [Что такое Service Fabric?](service-fabric-overview.md)
## [Основные сведения о микрослужбах](service-fabric-overview-microservices.md)
## [Сценарии приложений](service-fabric-application-scenarios.md)
## [Архитектура](service-fabric-architecture.md)
## [Терминология](service-fabric-technical-overview.md)
## [Дорожная карта для содержимого](service-fabric-content-roadmap.md)

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

# Практическое руководство
## Создание приложения
### [Примеры и сценарии](service-fabric-patterns-and-scenarios.md)
### Основы
#### [Модель приложения](service-fabric-application-model.md)
#### [Поддерживаемая модель программирования](service-fabric-choose-framework.md)
#### [Состояние службы](service-fabric-concepts-state.md)
#### [Взаимодействие служб](service-fabric-connect-and-communicate-with-services.md)
#### [Добавление веб-интерфейса](service-fabric-add-a-web-frontend.md)
#### [Ресурсы манифеста службы](service-fabric-service-manifest-resources.md)
#### [Управление приложениями в Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Настройка безопасных соединений в Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### Отладка
##### [Отладка службы на C# в VS](service-fabric-debugging-your-application.md)
##### [Отладка службы на Java в Eclipse](service-fabric-debugging-your-application-java.md)
#### Мониторинг и диагностика
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Управление секретами приложений](service-fabric-application-secret-management.md)  
#### [Настройка политик безопасности для приложения](service-fabric-application-runas-security.md)  
#### [Настройка приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md)  
#### [Распространенные ошибки и исключения](service-fabric-errors-and-exceptions.md) 

### Гостевое исполняемое приложение
#### [Развертывание гостевого исполняемого файла](service-fabric-deploy-existing-app.md)
#### [Развертывание нескольких пользовательских приложений](service-fabric-deploy-multiple-apps.md)

### Приложение-контейнер
#### [Обзор](service-fabric-containers-overview.md)
#### [Развертывание контейнера Windows](service-fabric-deploy-container.md)
#### [Развертывание контейнера Docker](service-fabric-deploy-container-linux.md)

### Приложение Reliable Services
#### [Обзор](service-fabric-reliable-services-introduction.md)
#### Начало работы
##### [C# в Windows](service-fabric-reliable-services-quick-start.md)
##### [Java в Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Архитектура](service-fabric-reliable-services-platform-architecture.md)
#### [Жизненный цикл Reliable Services](service-fabric-reliable-services-lifecycle.md)
#### [Надежные коллекции](service-fabric-reliable-services-reliable-collections.md)
#### [Использование надежных коллекций](service-fabric-work-with-reliable-collections.md)
#### [Настройка](service-fabric-reliable-services-configuration.md)
#### [Уведомления](service-fabric-reliable-services-notifications.md)
#### [Резервное копирование и восстановление](service-fabric-reliable-services-backup-restore.md)
#### [Обмен данными с помощью Reliable Services](service-fabric-reliable-services-communication.md)
#### [Безопасное взаимодействие с Reliable Services](service-fabric-reliable-services-secure-communication.md)
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
#### [Настройка параметров подключения](service-fabric-reliable-actors-fabrictransportsettings.md) 

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
#### [Управление кластером с помощью интерфейса командной строки Azure](service-fabric-azure-cli.md) 
#### [Обеспечение безопасности кластера](service-fabric-cluster-security.md)
#### [Аварийное восстановление](service-fabric-disaster-recovery.md)

### Кластеры в Azure
#### Создание кластера в Azure
##### [Портал Azure](service-fabric-cluster-creation-via-portal.md)
##### [Диспетчер ресурсов Azure](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio и Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### [Виды узлов и масштабируемые наборы ВМ](service-fabric-cluster-nodetypes.md)
#### [Масштабирование кластера](service-fabric-cluster-scale-up-down.md)
#### [Обновление кластера](service-fabric-cluster-upgrade.md)
#### [Удаление кластера](service-fabric-cluster-delete.md)
#### [Контроль доступа](service-fabric-cluster-security-roles.md)
#### [Настройка кластера](service-fabric-cluster-fabric-settings.md)
#### [Защита кластера с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)
#### [Добавление или изменение сертификатов кластера](service-fabric-cluster-security-update-certs-azure.md) 
#### [Пробное бесплатное использование кластера Party Cluster](http://aka.ms/tryservicefabric)

### Изолированные кластеры
#### [Планирование и подготовка развертывания](service-fabric-cluster-standalone-deployment-preparation.md)
#### [Содержимое изолированного пакета Service Fabric](service-fabric-cluster-standalone-package-contents.md)
#### [Создание изолированного кластера](service-fabric-cluster-creation-for-windows-server.md)
#### [Создание автономного кластера на виртуальной машине Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
#### [Масштабирование кластера](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Обновление кластера](service-fabric-cluster-upgrade-windows-server.md)
#### [Контроль доступа](service-fabric-cluster-security-roles.md)
#### [Настройка кластера](service-fabric-cluster-manifest.md)
#### [Защита кластера с помощью сертификатов](service-fabric-windows-cluster-x509-security.md)  
#### [Защита кластера с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md) 

## Управление жизненным циклом приложения
### [Обзор](service-fabric-application-lifecycle.md)
### [Настройка непрерывной интеграции](service-fabric-set-up-continuous-integration.md)
### [Общие сведения о параметре ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
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

## Проверка работоспособности приложения и кластера
### [Отслеживание работоспособности Service Fabric](service-fabric-health-introduction.md)
### [Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Добавление настраиваемых отчетов о работоспособности](service-fabric-report-health.md)
### [Устранение неполадок с помощью отчетов о работоспособности системы](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Просмотр отчетов о работоспособности](service-fabric-view-entities-aggregated-health.md)

## Мониторинг и диагностика
### [Приложения мониторинга и диагностики](service-fabric-diagnostics-overview.md)
### Мониторинг и диагностика состояния служб в локальной среде разработки
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Журналы системы диагностики Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Сбор журналов из процесса службы](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Диагностика в Reliable Services с отслеживанием состояния](service-fabric-reliable-services-diagnostics.md)
### [Диагностика в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
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
#### [Использование API-интерфейсов для изменения состояния узла](service-fabric-node-transition-apis.md)
### [Нагрузочный тест приложения](service-fabric-vso-load-test.md)

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

# Справочные материалы
## [PowerShell](//powershell/servicefabric/vlatest/servicefabric)
## [API Java](/java/api/microsoft.servicefabric.services)
## [.NET](/dotnet/api/microsoft.servicefabric.services)
## [REST](/rest/api/servicefabric)

# Ресурсы
## [Часто задаваемые вопросы о Service Fabric](service-fabric-common-questions.md)
## [Варианты поддержки Service Fabric](service-fabric-support.md)
## [Пример кода](http://aka.ms/servicefabricsamples)
## [Схема обучения](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Цены](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=service-fabric)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
