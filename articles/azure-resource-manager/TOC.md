# Обзор
## [Что такое Resource Manager?](resource-group-overview.md)
## [Поставщики Resource Manager, регионы, версии API и схемы](resource-manager-supported-services.md)
## [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](resource-manager-deployment-model.md)
## [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md)
## [Обзор управляемых приложений Azure](managed-application-overview.md)

# Начало работы
## [Экспорт шаблона](resource-manager-export-template.md)
## [Создание первого шаблона](resource-manager-create-first-template.md)
## [Использование Visual Studio с Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Примеры
## PowerShell
### [Развертывание шаблона](resource-manager-samples-powershell-deploy.md)

## Инфраструктура CLI Azure
### [Развертывание шаблона](resource-manager-samples-cli-deploy.md)

# Практическое руководство
## Создание шаблонов
### [Рекомендации по работе с шаблонами](resource-manager-template-best-practices.md)
### [Разделы шаблона](resource-group-authoring-templates.md)
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
### [Портал](resource-group-template-deploy-portal.md)
### [REST API](resource-group-template-deploy-rest.md)
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
### [Назначение политики с помощью портала](resource-manager-policy-portal.md)
### [Назначение политики с помощью скрипта](resource-manager-policy-create-assign.md)
### [Политики тегов ресурсов](resource-manager-policy-tags.md)
### [Политики хранения](resource-manager-policy-storage.md)
### [Политики виртуальных машин Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Политики виртуальных машин Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## Использование управляемых приложений
### [Создание и публикация управляемого приложения Azure](managed-application-publishing.md)
### [Использование управляемого приложения Azure](managed-application-consumption.md)
### [Создание определений пользовательского интерфейса](managed-application-createuidefinition-overview.md)

## Аудит
### [Просмотр журналов действий](resource-group-audit.md)
### [Просмотр операций развертывания](resource-manager-deployment-operations.md)

## Устранение неполадок
### [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md)

# Справочные материалы
## [Формат шаблона](/azure/templates/)
## [Функции шаблонов](resource-group-template-functions.md)
### [Функции массива и объекта](resource-group-template-functions-array.md)
### [Функция сравнения](resource-group-template-functions-comparison.md)
### [Функции развертывания](resource-group-template-functions-deployment.md)
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
## [Запросы на регулирование](resource-manager-request-limits.md)
## [Отслеживание асинхронных операций](resource-manager-async-operations.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
## [Обновления службы](https://azure.microsoft.com/updates/?product=azure-resource-manager)
