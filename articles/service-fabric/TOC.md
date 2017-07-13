


# Обзор


## [Что такое Service Fabric?](service-fabric-overview.md)


## [Основные сведения о микрослужбах](service-fabric-overview-microservices.md)


## [Общая картина](service-fabric-content-roadmap.md)


## [Сценарии приложений](service-fabric-application-scenarios.md)


## [Примеры и сценарии](service-fabric-patterns-and-scenarios.md)


## [Архитектура](service-fabric-architecture.md)


## [Терминология](service-fabric-technical-overview.md)



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


## [Развертывание приложений .NET в контейнере](service-fabric-host-app-in-a-container.md)


## [Создание первого кластера в Azure](service-fabric-get-started-azure-cluster.md)


## [Создание первого автономного кластера](service-fabric-get-started-standalone-cluster.md)


## [Создание первого приложения-контейнера](service-fabric-get-started-containers.md)



# Практическое руководство:


## Создание приложения
  


### Основные понятия


#### [Поддерживаемые модели программирования](service-fabric-choose-framework.md)


#### [Модель приложения](service-fabric-application-model.md)


#### [Модель размещения](service-fabric-hosting-model.md)


#### [Ресурсы манифеста службы](service-fabric-service-manifest-resources.md)


#### [Состояние службы](service-fabric-concepts-state.md)


#### [Секционирование службы](service-fabric-concepts-partitioning.md)


#### [Доступность служб](service-fabric-availability-services.md)


#### [Масштабируемость приложений](service-fabric-concepts-scalability.md)


#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)



### [Планирование производительности приложения](service-fabric-capacity-planning.md)



### Создание гостевой исполняемой службы


#### [Развертывание гостевого исполняемого файла](service-fabric-deploy-existing-app.md)


#### [Развертывание нескольких пользовательских приложений](service-fabric-deploy-multiple-apps.md)



### Создание службы контейнеров


#### [Обзор](service-fabric-containers-overview.md)


#### [Развертывание контейнера Windows](service-fabric-deploy-container.md)


#### [Развертывание контейнера Linux](service-fabric-deploy-container-linux.md)


#### [Docker Compose (предварительная версия)](service-fabric-docker-compose.md)


#### [Управление ресурсами для контейнеров и служб](service-fabric-resource-governance.md)


#### [Драйверы томов и драйверы ведения журналов](service-fabric-containers-volume-logging-drivers.md)



### Создание приложения Reliable Services


#### [Обзор](service-fabric-reliable-services-introduction.md)


#### Основные понятия


##### [Жизненный цикл Reliable Services (C#)](service-fabric-reliable-services-lifecycle.md)


##### [Жизненный цикл Reliable Services (Java)](service-fabric-reliable-services-lifecycle-java.md)



#### Reliable Collections


##### [Надежные коллекции](service-fabric-reliable-services-reliable-collections.md)


##### [Reliable Collections: инструкции и рекомендации](service-fabric-reliable-services-reliable-collections-guidelines.md)


##### [Работа с Reliable Collections](service-fabric-work-with-reliable-collections.md)


##### [Транзакции и блокировки](service-fabric-reliable-services-reliable-collections-transactions-locks.md)


##### [Надежная параллельная очередь](service-fabric-reliable-services-reliable-concurrent-queue.md)


##### [Сериализация надежной коллекции](service-fabric-reliable-services-reliable-collections-serialization.md)


##### [Внутренние компоненты Reliable State Manager и Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md)



#### Начало работы


##### [C# в Windows](service-fabric-reliable-services-quick-start.md)


##### [Java в Linux](service-fabric-reliable-services-quick-start-java.md)



#### [Настройка](service-fabric-reliable-services-configuration.md)


#### [Отправка уведомлений](service-fabric-reliable-services-notifications.md)


#### [Резервное копирование и восстановление](service-fabric-reliable-services-backup-restore.md)



#### Обмен данными со службами


##### [Обмен данными с помощью Reliable Services](service-fabric-reliable-services-communication.md)


##### [Удаленное взаимодействие со службой (C#)](service-fabric-reliable-services-communication-remoting.md)


##### [Удаленное взаимодействие со службой (Java)](service-fabric-reliable-services-communication-remoting-java.md)


##### [WCF](service-fabric-reliable-services-communication-wcf.md)


##### [Безопасные подключения — C#](service-fabric-reliable-services-secure-communication.md)


##### [Безопасные подключения — Java](service-fabric-reliable-services-secure-communication-java.md)



#### [Расширенное использование](service-fabric-reliable-services-advanced-usage.md)



### Создание службы Reliable Actors


#### [Обзор](service-fabric-reliable-actors-introduction.md)


#### Основные понятия


##### [Архитектура](service-fabric-reliable-actors-platform.md)


##### [Жизненный цикл и сборка мусора](service-fabric-reliable-actors-lifecycle.md)


##### [Управление данными о состоянии](service-fabric-reliable-actors-state-management.md)


##### [Полиморфизм](service-fabric-reliable-actors-polymorphism.md)


##### [Повторный вход](service-fabric-reliable-actors-reentrancy.md)


##### [Сериализация типа](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)



#### Начало работы


##### [C# в Windows](service-fabric-reliable-actors-get-started.md)


##### [Java в Linux](service-fabric-reliable-actors-get-started-java.md)



#### [Отправка уведомлений](service-fabric-reliable-actors-events.md) 


#### [Настройка таймеров и напоминаний](service-fabric-reliable-actors-timers-reminders.md)


#### [Настройка KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)


#### [Настройка параметров подключения](service-fabric-reliable-actors-fabrictransportsettings.md) 


#### [Настройка ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)



### Обмен данными со службами


#### [Взаимодействие служб](service-fabric-connect-and-communicate-with-services.md)


#### [Служба DNS](service-fabric-dnsservice.md)


#### [Обратный прокси-сервер](service-fabric-reverseproxy.md)


#### [Настройка обратного прокси-сервера для безопасного подключения](service-fabric-reverseproxy-configure-secure-communication.md)



### [Добавление веб-интерфейса](service-fabric-add-a-web-frontend.md)



### Работа в IDE


#### [Начало работы с подключаемым модулем Eclipse для разработки приложений Java](service-fabric-get-started-eclipse.md)


#### [Управление приложениями в Visual Studio](service-fabric-manage-application-in-visual-studio.md)


#### [Настройка безопасных соединений в Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)


#### [Настройка приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md)



### Настройка безопасности


#### [Управление секретами приложений](service-fabric-application-secret-management.md)  


#### [Настройка политик безопасности для приложения](service-fabric-application-runas-security.md)



### Отладка


#### [Отладка службы на C# в VS](service-fabric-debugging-your-application.md)


#### [Отладка службы на Java в Eclipse](service-fabric-debugging-your-application-java.md)


#### [Распространенные ошибки и исключения](service-fabric-errors-and-exceptions.md)



### Локальные мониторинг и диагностика


#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)


#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)



### Интеграция со службой управления API


#### [Обзор](service-fabric-api-management-overview.md)


#### [Краткое руководство](service-fabric-api-management-quick-start.md)



### Миграция из облачных служб


#### [Сравнение облачных служб и Service Fabric](service-fabric-cloud-services-migration-differences.md)


#### [Переход к использованию Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)


#### [Рекомендации](/azure/architecture/service-fabric/migrate-from-cloud-services)



## Управление жизненным циклом приложения


### [Обзор](service-fabric-application-lifecycle.md)


### [Создание пакета приложения](service-fabric-package-apps.md)


### [Общие сведения о параметре ImageStoreConnectionString](service-fabric-image-store-connection-string.md)


### Развертывание или удаление приложений


#### [PowerShell](service-fabric-deploy-remove-applications.md)


#### [Azure CLI 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md)


#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)


#### [API-интерфейсы FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)


### Обновление приложения


#### [Обзор](service-fabric-application-upgrade.md)


#### [Настройка обновления приложения](service-fabric-visualstudio-configure-upgrade.md)


#### [Параметры обновления приложений](service-fabric-application-upgrade-parameters.md)


#### Обновление


##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)


##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)


#### [Устранение неполадок при обновлениях приложений](service-fabric-application-upgrade-troubleshooting.md)


#### [Сериализация данных при обновлении приложения](service-fabric-application-upgrade-data-serialization.md)


#### [Обновление приложения: более сложные темы](service-fabric-application-upgrade-advanced.md)



### Тестирование приложений и служб


#### [Обзор анализа ошибок](service-fabric-testability-overview.md)


#### [Тестирование связи между службами](service-fabric-testability-scenarios-service-communication.md)


#### Моделирование сбоев


##### [Использование контролируемого хаоса](service-fabric-controlled-chaos.md)


##### [Использование тестовых действий](service-fabric-testability-actions.md)


##### [При выполнении рабочих нагрузок](service-fabric-testability-workload-tests.md)


##### [Использование тестовых сценариев](service-fabric-testability-scenarios.md)


##### [Использование API-интерфейсов для изменения состояния узла](service-fabric-node-transition-apis.md)


#### [Нагрузочный тест приложения](service-fabric-vso-load-test.md)



### Настройка непрерывной интеграции


#### [Настройка непрерывной интеграции с помощью VSTS](service-fabric-set-up-continuous-integration.md)


#### [Развертывание приложения Linux Java с помощью Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)



## Создание кластеров и управление ими



### [Обзор](service-fabric-deploy-anywhere.md)


### Основные понятия


#### [Описание кластера](service-fabric-cluster-resource-manager-cluster-description.md)


#### [Безопасность кластера](service-fabric-cluster-security.md)


#### [Различия между функциями Linux и Windows](service-fabric-linux-windows-differences.md)



### Планирование и подготовка


#### [Планирование ресурсов](service-fabric-cluster-capacity.md)


#### [Аварийное восстановление](service-fabric-disaster-recovery.md)



### Кластеры в Azure


#### Основные понятия


##### [Виды узлов и масштабируемые наборы ВМ](service-fabric-cluster-nodetypes.md)


##### [Шаблоны сети кластера](service-fabric-patterns-networking.md)


#### Создание 


##### [Портал Azure](service-fabric-cluster-creation-via-portal.md)


##### [Диспетчер ресурсов Azure](service-fabric-cluster-creation-via-arm.md)


##### [Visual Studio и Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)


#### Масштаб 


##### [Вручную](service-fabric-cluster-scale-up-down.md)


##### [Программным способом](service-fabric-cluster-programmatic-scaling.md)


#### [Обновление](service-fabric-cluster-upgrade.md)


#### [Настройка управления доступом](service-fabric-cluster-security-roles.md)


#### [Настройка](service-fabric-cluster-fabric-settings.md)


#### [Управление сертификатами кластера](service-fabric-cluster-security-update-certs-azure.md) 


#### [Удалить](service-fabric-cluster-delete.md)



### Изолированные кластеры


#### [Планирование и подготовка развертывания](service-fabric-cluster-standalone-deployment-preparation.md)


#### Создание


##### [Создание локальной среды](service-fabric-cluster-creation-for-windows-server.md)


##### [Создание в виртуальных машинах Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)


##### [Безопасное использование сертификатов](service-fabric-windows-cluster-x509-security.md)  


##### [Защита кластера с помощью системы безопасности Windows](service-fabric-windows-cluster-windows-security.md)


##### [Содержимое изолированного пакета](service-fabric-cluster-standalone-package-contents.md)


#### [Масштабирование](service-fabric-cluster-windows-server-add-remove-nodes.md)


#### [Настройка управления доступом](service-fabric-cluster-security-roles.md)


#### [Настройка](service-fabric-cluster-manifest.md)


#### [Обновление](service-fabric-cluster-upgrade-windows-server.md) 



### [Визуализация кластера](service-fabric-visualizing-your-cluster.md)


### [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md)



### [Управление кластером с помощью интерфейса командной строки XPlat](service-fabric-azure-cli.md)


### [Управление кластером с помощью команд Azure CLI 2.0 ](service-fabric-azure-cli-2-0.md)


### [Установка исправлений для узлов кластера](service-fabric-patch-orchestration-application.md)



### Ресурсы кластера: управление и оркестрация


#### [Обзор диспетчера кластерных ресурсов](service-fabric-cluster-resource-manager-introduction.md)


#### [Архитектура диспетчера кластерных ресурсов](service-fabric-cluster-resource-manager-architecture.md)


#### [Описание кластера](service-fabric-cluster-resource-manager-cluster-description.md)


#### [Обзор групп приложений](service-fabric-cluster-resource-manager-application-groups.md)


#### [Настройка параметров диспетчера кластерных ресурсов](service-fabric-cluster-resource-manager-configure-services.md)


#### [Метрики потребления ресурсов](service-fabric-cluster-resource-manager-metrics.md)


#### [Использование сходства служб](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)


#### [Политики размещения служб](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)


#### [Управление кластером](service-fabric-cluster-resource-manager-management-integration.md)


#### [Дефрагментация кластера](service-fabric-cluster-resource-manager-defragmentation-metrics.md)


#### [Балансировка кластера](service-fabric-cluster-resource-manager-balancing.md)


#### [Регулирование](service-fabric-cluster-resource-manager-advanced-throttling.md)


#### [Перемещение служб](service-fabric-cluster-resource-manager-movement-cost.md)



## Мониторинг и диагностика


### [Приложения мониторинга и диагностики](service-fabric-diagnostics-overview.md)


### Создание событий


#### [Создание событий на уровне инфраструктуры](service-fabric-diagnostics-event-generation-infra.md)


##### [События Reliable Services](service-fabric-reliable-services-diagnostics.md)


##### [События Reliable Actors](service-fabric-reliable-actors-diagnostics.md)


#### [Создание событий на уровне приложения](service-fabric-diagnostics-event-generation-app.md)


### Проверка работоспособности приложения и кластера


#### [Отслеживание работоспособности Service Fabric](service-fabric-health-introduction.md)


#### [Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)


#### [Добавление настраиваемых отчетов о работоспособности](service-fabric-report-health.md)


#### [Устранение неполадок с помощью отчетов о работоспособности системы](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)


#### [Просмотр отчетов о работоспособности](service-fabric-view-entities-aggregated-health.md)


### Статистическая обработка событий


#### [Статистическая обработка событий с помощью EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)


#### Статистическая обработка событий с помощью системы диагностики Azure


##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)


##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)


### Анализ событий


#### [Анализ событий с помощью Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)


#### [Анализ событий с помощью OMS](service-fabric-diagnostics-event-analysis-oms.md)


### [Устранение неполадок локального кластера](service-fabric-troubleshoot-local-cluster-setup.md)



# Справочные материалы


## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)


## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)


## [Интерфейс командной строки Azure](/cli/azure/sf)


## [API Java](/java/api/overview/azure/servicefabric)


## [.NET](/dotnet/api/overview/azure/servicefabric?view=azure-dotnet)


## [REST](/rest/api/servicefabric)



# Ресурсы


## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/)


## [Часто задаваемые вопросы о Service Fabric](service-fabric-common-questions.md)


## [Схема обучения](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)


## [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)


## [Цены](https://azure.microsoft.com/pricing/details/service-fabric/)


## [Пример кода](http://aka.ms/servicefabricsamples)


## [Варианты поддержки Service Fabric](service-fabric-support.md)


## [Обновления службы](https://azure.microsoft.com/updates/?product=service-fabric)


## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
