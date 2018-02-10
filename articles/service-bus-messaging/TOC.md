# Обзор
## [Что такое обмен сообщениями с помощью служебной шины?](service-bus-messaging-overview.md)
## [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
## [Архитектура служебной шины](service-bus-architecture.md)
## [Часто задаваемые вопросы](service-bus-faq.md)

# Быстрое начало работы
## [Создание пространства имен](service-bus-create-namespace-portal.md)
## Использование очередей
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Использование разделов и подписок
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)

# Практическое руководство
## Планирование и проектирование
### [Управляемые удостоверения службы (предварительная версия)](service-bus-managed-service-identity.md)
### [Управление доступом на основе ролей (предварительная версия)](service-bus-role-based-access-control.md)
### [Обмен сообщениями: уровень "Премиум"](service-bus-premium-messaging.md)
### [Сравнение очередей Azure и очередей служебной шины](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Оптимизация производительности](service-bus-performance-improvements.md)
### [Аварийное восстановление и георепликация](service-bus-geo-dr.md)
### [Асинхронный обмен сообщениями и высокий уровень доступности](service-bus-async-messaging.md)
### [Обработка сбоев и аварийных ситуаций](service-bus-outages-disasters.md)

## Разработка
### [Создание многоуровневого приложения служебной шины](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
### Обработка сообщений
#### [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
#### [Сообщения, полезные данные и сериализация](service-bus-messages-payloads.md)
#### [Передача, блокировка и согласование сообщений](message-transfers-locks-settlement.md)
#### [Последовательность сообщений и метки времени](message-sequencing.md)
#### [Срок действия сообщения (срок жизни)](message-expiration.md)
### [Аутентификация и авторизация](service-bus-authentication-and-authorization.md)
#### [Миграция из ACS в SAS](service-bus-migrate-acs-sas.md)
#### [Проверка подлинности с помощью подписанных URL-адресов](service-bus-sas.md)
### [Фильтры разделов и действия](topic-filters.md)
### [Секционированные очереди и разделы](service-bus-partitioning.md)
### [Сеансы обмена сообщениями](message-sessions.md)
### AMQP
#### [Обзор AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java Message Service и AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [Руководство по использованию протокола AMQP](service-bus-amqp-protocol-guide.md)
#### [AMQP: операции c запросами и ответами](service-bus-amqp-request-response.md)
### Дополнительные функции
#### [Очереди недоставленных сообщений](service-bus-dead-letter-queues.md)
#### [Предварительная выборка сообщений](service-bus-prefetch.md)
#### [Обнаружение повторяющихся сообщений](duplicate-detection.md)
#### [Счетчики сообщений](message-counters.md)
#### [Отсрочка сообщений](message-deferral.md)
#### [Просмотр сообщений](message-browsing.md)
#### [Объединение в цепочки сущностей с помощью автоматической пересылки](service-bus-auto-forwarding.md)
#### [Обработка транзакций](service-bus-transactions.md)
#### [Реализация сопряженных пространств имен](service-bus-paired-namespaces.md)
### [Всесторонняя трассировка и диагностика](service-bus-end-to-end-tracing.md)
## управление
### [Мониторинг служебной шины с помощью Azure Monitor](service-bus-metrics-azure-monitor.md)
### [Библиотеки управления служебной шиной](service-bus-management-libraries.md)
### [Журналы диагностики](service-bus-diagnostic-logs.md)
### [Приостановка и повторная активация сущностей обмена сообщениями](entity-suspend.md)
### [Использование шаблонов Azure Resource Manager](service-bus-resource-manager-overview.md)
#### [Создание пространства имен](service-bus-resource-manager-namespace.md)
#### [Создание пространства имен и очереди](service-bus-resource-manager-namespace-queue.md)
#### [Создание пространства имен с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
#### [Создание правила авторизации для пространства имен и очереди](service-bus-resource-manager-namespace-auth-rule.md)
#### [Создание пространства имен с разделом, подпиской и правилом](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Подготовка сущностей с помощью Azure PowerShell](service-bus-manage-with-ps.md)

# Справочные материалы
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Исключения](service-bus-messaging-exceptions.md)
## [Квоты](service-bus-quotas.md)
## [Синтаксис SQLFilter](service-bus-messaging-sql-filter.md)
## [Синтаксис SQLRuleAction](service-bus-messaging-sql-rule-action.md)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Блог](https://blogs.msdn.microsoft.com/servicebus/)
## [Форумы MSDN](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Цены](https://azure.microsoft.com/pricing/details/service-bus/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Сведения о тарифах](service-bus-pricing-billing.md)
## [Примеры](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Обозреватель служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Обновления службы](https://azure.microsoft.com/updates/?product=service-bus)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


