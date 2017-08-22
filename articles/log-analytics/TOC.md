# Обзор
## [Что такое Log Analytics?](log-analytics-overview.md)
## [Защита данных](log-analytics-security.md)

# Начало работы
## [Регистрация в службе Log Analytics](log-analytics-get-started.md)
## [Управление доступом](log-analytics-manage-access.md)
## [Данные об использовании](log-analytics-usage.md)
## [Часто задаваемые вопросы по Log Analytics](log-analytics-faq.md)
## [Поставщики услуг](log-analytics-service-providers.md)

# Практическое руководство
## Сбор данных
### Подключенные источники
#### [Агенты Windows](log-analytics-windows-agents.md)
#### [Агенты Linux](log-analytics-agent-linux.md)
#### [Виртуальные машины Azure](log-analytics-azure-vm-extension.md)
#### [Ресурсы Azure](log-analytics-azure-storage.md)
#### [Operations Manager](log-analytics-om-agents.md)
#### [Configuration Manager](log-analytics-sccm.md)
#### [Шлюз OMS](log-analytics-oms-gateway.md)
### Источники данных
#### [Обзор источников данных](log-analytics-data-sources.md)
#### [События Windows](log-analytics-data-sources-windows-events.md)
#### [Настраиваемые данные JSON](log-analytics-data-sources-json.md)
#### [Сбор данных о производительности](log-analytics-data-sources-collectd.md)
#### [Оповещения Nagios и Zabbix](log-analytics-data-sources-alerts-nagios-zabbix.md)
#### [Системный журнал](log-analytics-data-sources-syslog.md)
#### [Счетчики производительности](log-analytics-data-sources-performance-counters.md)
#### [Производительность приложений Linux](log-analytics-data-sources-linux-applications.md)
#### [Журналы IIS](log-analytics-data-sources-iis-logs.md)
#### [Пользовательские журналы](log-analytics-data-sources-custom-logs.md)
#### [Настраиваемые поля](log-analytics-custom-fields.md)

## Запрос данных
### [Обзор поиска по журналу](log-analytics-log-searches.md)
### [Справочник по поиску](log-analytics-search-reference.md)
#### [Регулярные выражения](log-analytics-log-searches-regex.md)
### [Выполнение действий на основе результатов поиска](log-analytics-log-search-takeaction.md)
### [Группы компьютеров](log-analytics-computer-groups.md)

## Новый поиск по журналам
### [Обновление рабочей области](log-analytics-log-search-upgrade.md)
### [Часто задаваемые вопросы](log-analytics-log-search-faq.md)
### [Обзор поиска по журналу](log-analytics-log-search-new.md)
### [Порталы поиска по журналам](log-analytics-log-search-portals.md)
#### [Использование портала поиска по журналам](log-analytics-log-search-log-search-portal.md)
### [Переход от устаревшего языка](log-analytics-log-search-transition.md)
### [Соединитель для Microsoft Flow](log-analytics-flow-tutorial.md)

## Анализ данных
### [Панели мониторинга](log-analytics-dashboards.md)
### [Конструктор представлений](log-analytics-view-designer.md)
### [Power BI](log-analytics-powerbi.md)
## Создание оповещений
### [Основные сведения об оповещениях](log-analytics-alerts.md)
### [Действия оповещений](log-analytics-alerts-actions.md)
### Создание правил генерации оповещений
#### [Портал OMS](log-analytics-alerts-creating.md)
#### [REST API](log-analytics-api-alerts.md)
#### [Шаблон Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
### [Пример действия webhook](log-analytics-alerts-webhooks.md)
### [Решение по управлению оповещениями](log-analytics-solution-alert-management.md)
## Использование решений
### [Обзор решений](log-analytics-add-solutions.md)
### [Целевые решения](../operations-management-suite/operations-management-suite-solution-targeting.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Анализ журнала действий](log-analytics-activity.md)
### [Оценка AD](log-analytics-ad-assessment.md)
### [Состояние репликации AD](log-analytics-ad-replication-status.md)
### [Работоспособность агентов](../operations-management-suite/oms-solution-agenthealth.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Управление оповещениями](log-analytics-solution-alert-management.md)
### [Соединитель Application Insights](log-analytics-app-insights-connector.md)
### [Azure SQL Analytics](log-analytics-azure-sql.md)
### [Аналитика веб-приложений Azure](log-analytics-azure-web-apps-analytics.md)
### [Емкость и производительность](log-analytics-capacity.md)
### [Отслеживание изменений](log-analytics-change-tracking.md)
### [Контейнеры](log-analytics-containers.md)
### [DNS Analytics](log-analytics-dns.md)
### [Соединитель управления ИТ-службами](log-analytics-itsmc-overview.md)
#### [Подключения управления ИТ-службами](log-analytics-itsmc-connections.md)
### [хранилище ключей;](log-analytics-azure-key-vault.md)
### Сообщения B2B для приложений логики
#### [Решение "Сообщения B2B для приложений логики"](../logic-apps/logic-apps-track-b2b-messages-omsportal.md?toc=%2fazure%2flog-analytics%2ftoc.json)
#### [Настраиваемая схема отслеживания сообщений B2B для приложений логики](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Оценка вредоносных программ](log-analytics-malware.md)
### [Анализ сетевой активности](log-analytics-azure-networking-analytics.md)
### [Монитор производительности сети](log-analytics-network-performance-monitor.md)
### [Office 365](../operations-management-suite/oms-solution-office-365.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Оценка SCOM](log-analytics-scom-assessment.md)
### [Аудит безопасности](../operations-management-suite/oms-security-getting-started.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Оценка Service Fabric с помощью PowerShell](log-analytics-service-fabric.md)
#### [Оценка Service Fabric с помощью Azure Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
### [Схема услуги](../operations-management-suite/operations-management-suite-service-map.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Оценка SQL](log-analytics-sql-assessment.md)
### [Surface Hub](log-analytics-surface-hubs.md)
### [Управление обновлениями](../operations-management-suite/oms-solution-update-management.md)
### [VMware](log-analytics-vmware.md)
### Аналитика Windows
#### [Соответствие обновлений](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started)
#### [Готовность к обновлению](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started)
### [Данные передачи](log-analytics-wire-data.md)
## Разработка
### [API сборщика данных](log-analytics-data-collector-api.md)
### [Командлеты PowerShell](log-analytics-powershell-workspace-configuration.md)
### [Шаблоны Resource Manager](log-analytics-template-workspace-configuration.md)
### [API поиска по журналам](log-analytics-log-search-api.md)
#### [Python](log-analytics-log-search-api-python.md)
### [API оповещений](log-analytics-api-alerts.md)

# Справочные материалы
## [PowerShell](/powershell/module/azurerm.operationalinsights)
## [REST](/rest/api/loganalytics)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/)
## [Цены](https://azure.microsoft.com/pricing/details/log-analytics/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=log-analytics)
## [Аналитика Windows](https://www.microsoft.com/en-us/WindowsForBusiness/windows-analytics)
