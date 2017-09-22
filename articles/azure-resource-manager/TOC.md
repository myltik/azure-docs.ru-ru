# Обзор
## [Что такое Resource Manager?](resource-group-overview.md)
## [Поставщики и типы ресурсов](resource-manager-supported-services.md)
## [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](resource-manager-deployment-model.md)
## [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md)
## [Обзор управляемых приложений Azure](managed-application-overview.md)

# Начало работы
## [Создание и развертывание шаблона](resource-manager-create-first-template.md)
## [Расширение VS Code для шаблонов](resource-manager-vscode-extension.md)
## [Использование Visual Studio с Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Примеры
## [Примеры кода](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## PowerShell
### [Развертывание шаблона](resource-manager-samples-powershell-deploy.md)

## Инфраструктура CLI Azure
### [Развертывание шаблона](resource-manager-samples-cli-deploy.md)

# Практическое руководство
## Создание шаблонов
### [Разделы шаблона](resource-group-authoring-templates.md)
### [Рекомендации по работе с шаблонами](resource-manager-template-best-practices.md)
### [Использование ссылок на другие шаблоны](resource-group-linked-templates.md)
### [Определение зависимости между ресурсами](resource-group-define-dependencies.md)
### [Развертывание нескольких экземпляров ресурсов в шаблонах Azure Resource Manager](resource-group-create-multiple.md)
### [Определение расположения](resource-manager-template-location.md)
### [Присвоение тегов](resource-manager-template-tags.md)
### [Настройка имени и типа дочернего ресурса](resource-manager-template-child-resource.md)
### [Обновление ресурса](resource-manager-update.md)
### [Использование объектов с параметрами](resource-manager-objects-as-parameters.md)
### [Обмен данными о состоянии между связанными шаблонами](best-practices-resource-manager-state.md)
### [Схемы проектирования шаблонов](best-practices-resource-manager-design-templates.md)


## Развернуть
### PowerShell
#### [Развертывание шаблона](resource-group-template-deploy.md)
#### [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md)
#### [Экспорт шаблона и повторное развертывание](resource-manager-export-template-powershell.md)
### Инфраструктура CLI Azure
#### [Развертывание шаблона](resource-group-template-deploy-cli.md)
#### [Развертывание частного шаблона с помощью маркера SAS](resource-manager-cli-sas-token.md)
#### [Экспорт шаблона и повторное развертывание](resource-manager-export-template-cli.md)
### Microsoft Azure
#### [Развертывание ресурсов](resource-group-template-deploy-portal.md)
#### [Экспорт шаблона](resource-manager-export-template.md)
### [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)
### [Развертывание в нескольких группах ресурсов](resource-manager-cross-resource-group-deployment.md)
### [Непрерывная интеграция с Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md)

## Управление
### [PowerShell](powershell-azure-resource-manager.md)
### [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)
### [Портал](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [Организация ресурсов с помощью тегов](resource-group-using-tags.md)
### [Перемещение ресурсов в новую группу или подписку](resource-group-move-resources.md)
### [Примеры реализации корпоративного каркаса Azure](resource-manager-subscription-examples.md)

## Контроль доступа
### Создание субъекта-службы
#### [PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [Портал](resource-group-create-service-principal-portal.md)
### [API проверки подлинности для доступа к подпискам](resource-manager-api-authentication.md)
### [Блокировка ресурсов](resource-group-lock-resources.md)

## Задание политик ресурсов
### [Что представляют собой политики ресурсов?](resource-manager-policy.md)
### [Назначение политик с помощью портала](resource-manager-policy-portal.md)
### [Назначение политик с помощью скриптов](resource-manager-policy-create-assign.md)
### Примеры
#### [Теги](resource-manager-policy-tags.md)
#### [Соглашения об именовании](resource-manager-policy-naming-convention.md)
#### [Сеть](resource-manager-policy-network.md)
#### [Хранилище](resource-manager-policy-storage.md)
#### [Виртуальные машины Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Виртуальные машины Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## Использование управляемых приложений
### [Публикация приложения из каталога служб](managed-application-publishing.md)
### [Использование приложения из каталога служб](managed-application-consumption.md)
### [Публикация приложения из Marketplace](managed-application-author-marketplace.md)
### [Использование приложения из Marketplace](managed-application-consume-marketplace.md)
### [Создание определений пользовательского интерфейса](managed-application-createuidefinition-overview.md)

## Аудит
### [Просмотр журналов действий](resource-group-audit.md)
### [Просмотр операций развертывания](resource-manager-deployment-operations.md)

## Устранение неполадок
### [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md)
### [Сведения об ошибках развертывания](resource-manager-troubleshoot-tips.md)
### Устранение ошибок
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md);
#### [InvalidTemplate](resource-manager-invalid-template-errors.md);
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md);
#### [NotFound](resource-manager-not-found-errors.md);
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md);
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md).
### Ошибки развертывания виртуальной машины
#### Linux
##### [Проблемы с развертыванием](../virtual-machines/linux/troubleshoot-deploy-vm.md)
##### [Проблемы подготовки и распределения](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
##### [Распространенные сообщения об ошибках](../virtual-machines/linux/error-messages.md)
#### Windows
##### [Проблемы с развертыванием](../virtual-machines/windows/troubleshoot-deploy-vm.md)
##### [Проблемы подготовки и распределения](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
##### [Распространенные сообщения об ошибках](../virtual-machines/windows/error-messages.md)

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
## [Функции определения пользовательского интерфейса](managed-application-createuidefinition-functions.md)
## [Элементы CreateUiDefinition](managed-application-createuidefinition-elements.md)
### [Элемент пользовательского интерфейса Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Элемент пользовательского интерфейса Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Элемент пользовательского интерфейса Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Элемент пользовательского интерфейса Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Элемент пользовательского интерфейса Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Элемент пользовательского интерфейса Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Элемент пользовательского интерфейса Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Элемент пользовательского интерфейса Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Элемент пользовательского интерфейса Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Элемент пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Элемент пользовательского интерфейса Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Элемент пользовательского интерфейса Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Элемент пользовательского интерфейса Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Интерфейс командной строки Azure](/cli/azure/resource)
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
