# [Документация по Azure Resource Manager](index.md)

# Обзор
## [Что такое Resource Manager?](resource-group-overview.md)
## [Поставщики и типы ресурсов](resource-manager-supported-services.md)
## [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](resource-manager-deployment-model.md)
## [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md)
## [Упорядочение ресурсов с помощью групп управления](management-groups-overview.md)

# Начало работы
## [Создание и развертывание шаблона](resource-manager-create-first-template.md)
## [Расширение VS Code для шаблонов](resource-manager-vscode-extension.md)
## [Использование Visual Studio с Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Практическое руководство
## Создание шаблонов
### [Разделы шаблона](resource-group-authoring-templates.md)
#### [Параметры](resource-manager-templates-parameters.md)
#### [Переменные](resource-manager-templates-variables.md)
#### [Ресурсы](resource-manager-templates-resources.md)
#### [Outputs](resource-manager-templates-outputs.md)
### [Связанные и вложенные шаблоны](resource-group-linked-templates.md)
### [Определение зависимости между ресурсами](resource-group-define-dependencies.md)
### [Развертывание нескольких экземпляров ресурсов в шаблонах Azure Resource Manager](resource-group-create-multiple.md)
### [Обновление ресурса](/azure/architecture/building-blocks/extending-templates/update-resource)

## Развертывание
### Azure PowerShell
#### [Развертывание шаблона](resource-group-template-deploy.md)
#### [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md)
#### [Экспорт шаблона и повторное развертывание](resource-manager-export-template-powershell.md)
### Инфраструктура CLI Azure
#### [Развертывание шаблона](resource-group-template-deploy-cli.md)
#### [Развертывание частного шаблона с помощью маркера SAS](resource-manager-cli-sas-token.md)
#### [Экспорт шаблона и повторное развертывание](resource-manager-export-template-cli.md)
### Портал Azure
#### [Развертывание ресурсов](resource-group-template-deploy-portal.md)
#### [Экспорт шаблона](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [Несколько подписок или групп ресурсов](resource-manager-cross-resource-group-deployment.md)
### [Непрерывная интеграция с Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md)

## управление
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)
### [портал Azure](resource-group-portal.md)
### [Организация ресурсов с помощью тегов](resource-group-using-tags.md)
### [Перемещение ресурсов в новую группу или подписку](resource-group-move-resources.md)
### [Создание подписок EA программными средствами](programmatically-create-subscription.md)
### [Создание групп управления](management-groups-create.md)
### [Администрирование групп управления](management-groups-manage.md)
### [Примеры реализации корпоративного каркаса Azure](resource-manager-subscription-examples.md)

## Контроль доступа
### Создание субъекта-службы
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [интерфейс командной строки Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [портал Azure](resource-group-create-service-principal-portal.md)
### [API проверки подлинности для доступа к подпискам](resource-manager-api-authentication.md)
### [Блокировка ресурсов](resource-group-lock-resources.md)

## Аудит
### [Просмотр журналов действий](resource-group-audit.md)
### [Просмотр операций развертывания](resource-manager-deployment-operations.md)

## Устранение неполадок
### [Устранение ошибок развертывания](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md);
### [InvalidTemplate](resource-manager-invalid-template-errors.md);
### [Проблемы с развертыванием в Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md);
### [NotFound](resource-manager-not-found-errors.md);
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Проблемы подготовки и распределения в Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Проблемы подготовки и распределения в Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md);
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md).
### [Проблемы с развертыванием в Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# Справочные материалы
## [Формат шаблона](/azure/templates/)
## [Функции шаблонов](resource-group-template-functions.md)
### [Функции массива и объекта](resource-group-template-functions-array.md)
### [Функция сравнения](resource-group-template-functions-comparison.md)
### [Функции развертывания](resource-group-template-functions-deployment.md)
### [Логические функции](resource-group-template-functions-logical.md)
### [Числовые функции](resource-group-template-functions-numeric.md)
### [Функции для работы с ресурсами](resource-group-template-functions-resource.md)
### [Строковые функции](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [интерфейс командной строки Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Запросы на регулирование](resource-manager-request-limits.md)
## [Отслеживание асинхронных операций](resource-manager-async-operations.md)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
