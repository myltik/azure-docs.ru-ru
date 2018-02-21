
# [Документация по пакетной службе](index.yml)

# Обзор
## [Что такое пакетная служба Azure](batch-technical-overview.md)

# Быстрое начало работы
## [Выполнение пакетного задания — CLI](quick-create-cli.md)
## [Выполнение пакетного задания — портал](quick-create-portal.md)
## [Выполнение пакетного задания — .NET](quick-run-dotnet.md)
## [Выполнение пакетного задания — Python](quick-run-python.md)


# Учебники

## [Параллельная обработка файлов — .NET](tutorial-parallel-dotnet.md)
## [Параллельная обработка файлов — Python](tutorial-parallel-python.md)
## [Отрисовка сцен с использованием Arnold](tutorial-rendering-cli.md)
## [Параллельное моделирование на R](tutorial-r-doazureparallel.md)

# Примеры
## Примеры кода
### [Примеры кода Azure](https://azure.microsoft.com/resources/samples/?service=batch)
### [Репозиторий образцов пакетной службы](https://github.com/Azure/azure-batch-samples)
## [интерфейс командной строки Azure](cli-samples.md)

# Основные понятия
## [Возможности разработчика](batch-api-basics.md)
## [API-интерфейсы и средства](batch-apis-tools.md)
## [Квоты и ограничения](batch-quota-limit.md)

# Практические руководства
## Управление учетными записями пакетной службы
### [Управление учетными записями пакетной службы с помощью портала](batch-account-create-portal.md)
### [Управление учетными записями пакетной службы с помощью .NET для управления пакетной службой](batch-management-dotnet.md)

## Проверка подлинности с помощью Azure AD
### [Azure AD и пакетная служба](batch-aad-auth.md)
### [Azure AD и управление пакетной службой](batch-aad-auth-management.md)

## [Создание пакетов приложений](batch-application-packages.md)

## Создание пулов и управление ими
### [Автомасштабирование вычислительных узлов](batch-automatic-scaling.md)
### [Виртуальные машины для ресурсоемких вычислений](batch-pool-compute-intensive-sizes.md)
### [Настройка доступа к вычислительным узлам](pool-endpoint-configuration.md)
### [Создание пула с помощью пользовательского образа](batch-custom-images.md)
### [Создание пула в виртуальной сети](batch-virtual-network.md)
### [Вычислительные узлы Linux](batch-linux-nodes.md)
### [Виртуальные машины с низким приоритетом](batch-low-pri-vms.md)

## Управление заданиями и задачами
### [Задачи для подготовки и выполнения заданий](batch-job-prep-release.md)
### [Параллельные задачи узла](batch-parallel-node-tasks.md)
### [Подсчет задач по состоянию](batch-get-task-counts.md)
### [Зависимости задачи](batch-task-dependencies.md)
### [Учетные записи пользователей для выполнения задач](batch-user-accounts.md)
### [Выполнение пакетных заданий без кода](batch-cli-templates.md)


## [Сохранение выходных данных заданий и задач](batch-task-output.md)
### [Сохранение выходных данных с помощью API пакетной службы](batch-task-output-files.md)
### [Сохранение выходных данных с помощью библиотеки соглашений](batch-task-output-file-conventions.md)

## Выполнение рабочих нагрузок
### [Задания MPI](batch-mpi.md)
### [Контейнерные рабочие нагрузки](batch-docker-container-workloads.md)
### [Отрисовка](batch-rendering-service.md)
### [Обработка данных с помощью пакетной службы и фабрики данных](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)

## Мониторинг ресурсов
### [Журнал ведения диагностики](batch-diagnostics.md)
### [Эффективные запросы на вывод списка](batch-efficient-list-queries.md)

## Использование средств для разработчиков
### [Использование .NET для пакетной службы](batch-dotnet-get-started.md)
### [Использование Python для пакетной службы](batch-python-tutorial.md)
### [Использование Node.js для пакетной службы](batch-nodejs-get-started.md)
### [Использование командлетов PowerShell](batch-powershell-cmdlets-get-started.md)
### [Использование Azure CLI](batch-cli-get-started.md)

# Справочные материалы

## [интерфейс командной строки Azure](/cli/azure/batch)
## [Azure PowerShell](/powershell/module/azurerm.batch)
## [.NET](/dotnet/api/microsoft.azure.batch)
## [Java](/java/api/com.microsoft.azure.batch)
## [Node.js](/nodejs/api/overview/azure/batch)
## [Python](/python/api/overview/azure/batch)
## REST
### [Пакетная служба](/rest/api/batchservice)
### [Управление пакетной службой](/rest/api/batchmanagement/)
## [Пакетная аналитика](batch-analytics.md)
### [Событие создания пула](batch-pool-create-event.md)
### [Событие начала удаления пула](batch-pool-delete-start-event.md)
### [Событие завершения удаления пула](batch-pool-delete-complete-event.md)
### [Событие начала изменения размера пула](batch-pool-resize-start-event.md)
### [Событие завершения изменения размера пула](batch-pool-resize-complete-event.md)
### [Событие начала выполнения задачи](batch-task-start-event.md)
### [Событие завершения выполнения задачи](batch-task-complete-event.md)
### [Событие сбоя выполнения задачи](batch-task-fail-event.md)
## [Переменные среды вычислительного узла пакетной службы Azure](batch-compute-node-environment-variables.md)
### [Шаблон Resource Manager](/templates/microsoft.batch/batchaccounts)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/)
## [Форум по Пакетной службе](https://social.msdn.microsoft.com/Forums/en-us/home?forum=azurebatch)
## [Batch Shipyard](https://github.com/Azure/batch-shipyard)
## [Блог](https://blogs.technet.microsoft.com/windowshpc/)
## [Решения HPC в Azure](../virtual-machines/linux/high-performance-computing.md?toc=%2fazure%2fbatch%2ftoc.json)
## [Цены](https://azure.microsoft.com/pricing/details/batch/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=batch&updatetype=&platform=)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-batch)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=batch)


