# Архитектура

## Основные сведения об облаке

### [Проектирование отказоустойчивых приложений](guidance-resiliency-overview.md)
#### [Контрольный список для обеспечения устойчивости](guidance-resiliency-checklist.md)
#### [Анализ режима сбоя](guidance-resiliency-failure-mode-analysis.md)
#### [Аварийное восстановление](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Аварийное восстановление и высокий уровень доступности](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Высокая доступность](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Контрольный список для обеспечения высокой доступности](..\resiliency\resiliency-high-availability-checklist.md)
#### [Руководство по обеспечению устойчивости службы Microsoft Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Восстановление данных после повреждения или случайного удаления](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Восстановление после локальных сбоев](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Восстановление после прерывания работы служб во всем регионе](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Восстановление из локальной среды в Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Техническое руководство по обеспечению устойчивости в Azure](..\resiliency\resiliency-technical-guidance.md)


## Эталонные образцы архитектуры

### [Выполнение рабочих нагрузок виртуальных машин в Azure](guidance-ra-compute.md)
#### [Запуск виртуальной машины Linux в Azure](guidance-compute-single-vm-linux.md)
#### [Запуск виртуальной машины Windows в Azure](guidance-compute-single-vm.md)
#### [Запуск нескольких виртуальных машин в Azure для обеспечения масштабируемости и доступности](guidance-compute-multi-vm.md)
#### [Запуск виртуальных машин Linux в N-уровневой архитектуре](guidance-compute-n-tier-vm-linux.md)
#### [Запуск виртуальных машин Windows в N-уровневой архитектуре](guidance-compute-n-tier-vm.md)
#### [Запуск виртуальных машин Linux в нескольких регионах для обеспечения высокой доступности](guidance-compute-multiple-datacenters-linux.md)
#### [Запуск виртуальных машин Windows в нескольких регионах для обеспечения высокой доступности](guidance-compute-multiple-datacenters.md)

### [Подключение локальной сети к Azure](guidance-ra-hybrid-networking.md)
#### [Реализация гибридной сетевой архитектуры с помощью Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Реализация гибридной сетевой архитектуры с помощью Azure и локальной виртуальной частной сети](guidance-hybrid-network-vpn.md)
#### [Архитектура высокодоступной гибридной сети](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Защита границ облака в Azure](guidance-ra-network-security.md)
#### [Защита гибридной сетевой архитектуры в Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Сеть периметра между Azure и Интернетом](guidance-iaas-ra-secure-vnet-dmz.md)

### [Управление удостоверениями в Azure](guidance-ra-identity.md)
#### [Внедрение Azure Active Directory](guidance-identity-aad.md)
#### [Расширение доменных служб Active Directory в Azure](guidance-identity-adds-extend-domain.md)
#### [Создание леса ресурсов доменных служб Active Directory в Azure](guidance-identity-adds-resource-forest.md)
#### [Внедрение AD FS в Azure](guidance-identity-adfs.md)

### [Архитектуры веб-приложений для службы приложений Azure](guidance-ra-app-service.md)
#### [Базовое веб-приложение](guidance-web-apps-basic.md)
#### [Веб-приложение с высоким уровнем доступности](guidance-web-apps-multi-region.md)
#### [Повышение масштабируемости в веб-приложении](guidance-web-apps-scalability.md)


## Советы и рекомендации для облачных приложений

### [Руководство по проектированию API](..\best-practices-api-design.md)
### [Руководство по реализации API](..\best-practices-api-implementation.md)
### [Руководство по автоматическому масштабированию](..\best-practices-auto-scaling.md)
### [Контрольный список для обеспечения доступности](..\best-practices-availability-checklist.md)
### [Руководство по фоновым заданиям](..\best-practices-background-jobs.md)
### [Сопряженные регионы Azure](..\best-practices-availability-paired-regions.md)
### [Руководство по кэшированию](..\best-practices-caching.md)
### [Руководство по сети доставки содержимого](..\best-practices-cdn.md)
### [Руководство по секционированию данных](..\best-practices-data-partitioning.md)
### [Руководство по мониторингу и диагностике](..\best-practices-monitoring.md)
### [Облачные службы (Майкрософт) и сетевая безопасность](..\best-practices-network-security.md)
### [Рекомендации по разработке шаблонов Azure Resource Manager](..\best-practices-resource-manager-design-templates.md)
### [Рекомендуемые соглашения об именовании для ресурсов Azure](guidance-naming-conventions.md)
### [Вопросы безопасности при работе с Azure Resource Manager](..\best-practices-resource-manager-security.md)
### [Совместное использование состояния в шаблонах Azure Resource Manager](..\best-practices-resource-manager-state.md)
### [Общие рекомендации по повторным попыткам](..\best-practices-retry-general.md)
### [Конкретные рекомендации по использованию механизма повторов](..\best-practices-retry-service-specific.md)
### [Контрольный список для обеспечения масштабируемости](..\best-practices-scalability-checklist.md)


## Руководства по сценариям

### [Руководство по работе с ElasticSearch в Azure](guidance-elasticsearch.md)
#### [Запуск Elasticsearch в Azure](guidance-elasticsearch-running-on-azure.md)
#### [Настройка производительности приема данных](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Настройка агрегирования данных и производительности запросов](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Настройка отказоустойчивости и восстановления](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Создание среды для тестирования производительности](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Реализация плана тестирования JMeter](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Развертывание дискретизатора JMeter JUnit](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Выполнение автоматизированных тестов отказоустойчивости](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Выполнение автоматизированных тестов производительности](guidance-elasticsearch-running-automated-performance-tests.md)

### [Управление удостоверениями для мультитенантных приложений](guidance-multitenant-identity.md)
#### [Обзор](guidance-multitenant-identity-intro.md)
#### [Приложение Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Аутентификация с помощью Azure AD и OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Удостоверения на основе утверждений](guidance-multitenant-identity-claims.md)
#### [Регистрация и адаптации клиента](guidance-multitenant-identity-signup.md)
#### [Роли приложений](guidance-multitenant-identity-app-roles.md)
#### [Авторизация на основе ролей и ресурсов](guidance-multitenant-identity-authorize.md)
#### [Защита интерфейсного веб-API](guidance-multitenant-identity-web-api.md)
#### [Кэширование маркеров доступа](guidance-multitenant-identity-token-cache.md)
#### [Федерация со службой AD FS клиента](guidance-multitenant-identity-adfs.md)
#### [Использование утверждения клиента для получения маркеров доступа](guidance-multitenant-identity-client-assertion.md)
#### [Использование хранилища ключей Azure для защиты секретов приложений](guidance-multitenant-identity-keyvault.md)
#### [Развертывание виртуальных устройств с высоким уровнем доступности](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


