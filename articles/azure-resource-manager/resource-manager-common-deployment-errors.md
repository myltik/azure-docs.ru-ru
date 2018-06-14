---
title: Устранение распространенных ошибок развертывания в Azure | Документация Майкрософт
description: Описывается устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: ошибка развертывания, развертывание Azure, развернуть в Azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 3ecc1a9557c7854a0771decb3cc7f7597bcd87dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360025"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager | Microsoft Azure

В этой статье описаны некоторые распространенные ошибки при развертывании в Azure и предоставлены сведения об их устранении. Если не удается найти код ошибки развертывания, см. раздел [Поиск кода ошибки](#find-error-code).

## <a name="error-codes"></a>Коды ошибок

| Код ошибки | Устранение | Дополнительные сведения |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Следуйте ограничениям для имен учетных записей хранения. | [Устранение ошибок, связанных с именами учетных записей хранения](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Проверьте доступные свойства учетной записи хранения. | [Справочник по шаблонам Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Кластер или регион не имеют доступных ресурсов или не поддерживают запрашиваемый размер виртуальной машины. Повторите запрос позже или укажите другой размер виртуальной машины. | Проблемы подготовки и распределения для [Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) и [Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md), и [устранение ошибок выделения ресурсов](../virtual-machines/windows/allocation-failure.md)|
| AnotherOperationInProgress | Дождитесь завершения параллельной операции. | |
| AuthorizationFailed | Учетная запись или субъект-служба не имеют необходимых прав доступа для выполнения развертывания. Проверьте роль, к которой принадлежит учетная запись, и ее права доступа к области развертывания. | [Управление доступом на основе ролей в Azure](../role-based-access-control/role-assignments-portal.md) |
| BadRequest | Отправленные значения развертывания не соответствуют значениям, ожидаемым Resource Manager. Проверьте внутреннее сообщение о состоянии. Оно поможет вам в устранении неполадки. | [Справочник по шаблону](/azure/templates/) и [поддерживаемые расположения](resource-manager-templates-resources.md#location) |
| Конфликт | Запрашиваемая операция не разрешена в текущем состоянии ресурса. Например, изменение размера диска разрешено только при создании или освобождении виртуальной машины. | |
| DeploymentActive | Дождитесь завершения параллельного развертывания в эту группу ресурсов. | |
| DeploymentFailed | Ошибка DeploymentFailed является общей ошибкой, которая не содержит сведений, необходимых для ее устранения. Найдите в сведениях об ошибке ее код, с помощью которого можно получить дополнительные сведения. | [Поиск кода ошибки](#find-error-code) |
| DeploymentQuotaExceeded | Если вы достигли предела в 800 развертываний на группу ресурсов, удалите из журнала те развертывания, которые больше не нужны. Вы можете удалить записи из журнала с помощью команды [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) в Azure CLI или с помощью командлета [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) в PowerShell. Удаление записи из журнала развертывания не влияет на развертывание ресурсов. | |
| DnsRecordInUse | Имя записи DNS должно быть уникальным. Предоставьте другое имя или измените имеющуюся запись. | |
| ImageNotFound | Проверьте параметры образа виртуальной машины. |  |
| InUseSubnetCannotBeDeleted | Эта ошибка возникает, когда вы пытаетесь обновить ресурс, но при обработке запроса удаляется и создается ресурс. Укажите все неизменяемые значения. | [Обновление ресурса](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Получите маркер доступа для соответствующего клиента. Маркер можно получить только из клиента, которому принадлежит учетная запись. | |
| InvalidContentLink | скорее всего была предпринята попытка связать недоступный вложенный шаблон. Внимательно проверьте URI, указанный для вложенного шаблона. Если шаблон существует в учетной записи хранения, убедитесь, что URI доступен. Возможно, понадобится передать маркер SAS. | [Связанные шаблоны](resource-group-linked-templates.md) |
| InvalidParameter | Одно из значений, предоставленных для ресурса, не соответствует ожидаемому значению. Эта ошибка может возникнуть в результате многих различных состояний. Например, пароля может быть недостаточно или имя большого двоичного объекта может быть неверным. Проверьте сообщение об ошибке, чтобы определить, какое значение необходимо исправить. | |
| InvalidRequestContent | Среди значений развертывания есть неожидаемые значения либо обязательные значения отсутствуют. Проверьте значения для типа ресурса. | [Справочник по шаблонам](/azure/templates/) |
| InvalidRequestFormat | Включите ведение журнала отладки при выполнении развертывания и проверьте содержимое запроса. | [Ведение журнала отладки](#enable-debug-logging) |
| InvalidResourceNamespace | Проверьте пространство имен ресурсов, заданное в свойстве **type**. | [Справочник по шаблонам](/azure/templates/) |
| InvalidResourceReference | Ресурс не существует, или на него неверно ссылаются. Проверьте, следует ли добавить зависимость. Убедитесь, что для функции **reference** указаны параметры, необходимые для вашего сценария. | [Устранение ошибок, связанных с зависимостями](resource-manager-not-found-errors.md) |
| InvalidResourceType | Проверьте тип ресурсов, заданный в свойстве **type**. | [Справочник по шаблонам](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Зарегистрируйте подписку в поставщике ресурсов. | [Устранение ошибок регистрации](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Проверьте синтаксис шаблона на наличие ошибок. | [Устранение ошибок, связанных с недопустимым шаблоном](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Удалите ненужные зависимости. | [Устранение циклических зависимостей](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Проверьте, принадлежит ли учетная запись к тому же клиенту, что и группа ресурсов, в которую выполняется развертывание. | |
| LinkedInvalidPropertyId | Идентификатор ресурса не удается разрешить правильно. Убедитесь, что указаны все обязательные значения для идентификатора ресурса, включая идентификатор подписки, имя группы ресурсов, тип ресурса, имя родительского ресурса (если необходимо) и имя ресурса. | |
| LocationRequired | Укажите расположение ресурса. | [Определение расположения](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Убедитесь, что имя и тип вложенного ресурса содержат правильное количество сегментов. | [Разрешение сегментов ресурса](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Проверьте состояние регистрации поставщика ресурсов и поддерживаемые расположения. | [Устранение ошибок регистрации](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Зарегистрируйте подписку в поставщике ресурсов. | [Устранение ошибок регистрации](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Проверьте состояние регистрации поставщика ресурсов. | [Устранение ошибок регистрации](resource-manager-register-provider-errors.md) |
| NotFound | Возможно, вы пытаетесь развернуть зависимый ресурс параллельно с родительским ресурсом. Проверьте, не нужно ли добавить зависимость. | [Устранение ошибок, связанных с зависимостями](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Развертывание пытается выполнить операцию, которая превышает квоту для подписки, группы ресурсов или региона. Если это возможно, измените развертывание, чтобы не превышать квоты. В противном случае запросите изменение квот. | [Устранение ошибок квот ресурсов](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Убедитесь, что имеется родительский ресурс, прежде чем создавать дочерние ресурсы. | [Устранение ошибок, связанных с родительскими ресурсами](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | Указанный IP-адрес включает диапазон адресов, необходимый Azure. Измените IP-адрес, чтобы не использовать зарезервированный диапазон. | [IP-адреса](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Указанный IP-адрес находится вне диапазона подсети. Измените IP-адрес, чтобы он находился в пределах диапазона подсети. | [IP-адреса](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Некоторые свойства нельзя изменить в развернутом ресурсе. При обновлении ресурса измените только допустимые свойства. | [Обновление ресурса](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Подписка включает в себя политику ресурсов, предотвращающую действие, которое вы пытаетесь выполнить во время развертывания. Найдите политику, которая блокирует действие. Измените развертывание в соответствии с ограничениями политики, если это возможно. | [Устранение ошибок с политиками](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Укажите ресурс, имя которого не включает в себя зарезервированное имя. | [Зарезервированные имена ресурсов](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Дождитесь завершения удаления. | |
| ResourceGroupNotFound | Проверьте имя целевой группы ресурсов для развертывания. Оно уже должно существовать в подписке. Проверьте контекст подписки. | [Azure CLI](/cli/azure/account?#az_account_set), [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Развертывание ссылается на ресурс, который не может быть разрешен. Убедитесь, что для функции **reference** указаны параметры, необходимые для вашего сценария. | [Устранение ошибок с поиском ресурсов](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Развертывание пытается создать ресурсы, которые превышают квоту для подписки, группы ресурсов или региона. Если возможно, измените инфраструктуру, чтобы не превышать квоты. В противном случае запросите изменение квот. | [Устранение ошибок квот ресурсов](resource-manager-quota-errors.md) |
| SkuNotAvailable | Выберите SKU (например, размер виртуальной машины), который доступен в выбранном расположении. | [Устранение ошибок, связанных с недоступностью номера SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Предоставьте уникальное имя учетной записи хранения. | [Устранение ошибок, связанных с именами учетных записей хранения](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Предоставьте уникальное имя учетной записи хранения. | [Устранение ошибок, связанных с именами учетных записей хранения](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Проверьте подписку, группу ресурсов и имя учетной записи хранения, которую вы хотите использовать. | |
| SubnetsNotInSameVnet | Виртуальная машина может иметь только одну виртуальную сеть. При развертывании нескольких сетевых адаптеров убедитесь, что они принадлежат той же виртуальной сети. | [Использование нескольких сетевых адаптеров](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Удалите ненужные зависимости. | [Устранение циклических зависимостей](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Уменьшите количество групп ресурсов для одного развертывания. | [Развертывание в нескольких группах ресурсов](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Поиск кода ошибки

Существует два типа ошибок, которые могут произойти:

* ошибки проверки;
* ошибки развертывания.

Ошибки проверки возникают в сценариях, которые можно определить перед развертыванием. Это синтаксические ошибки в шаблоне или попытки развертывания ресурсов, которые приведут к превышению квот для подписки. Ошибки развертывания возникают из-за условий, возникающих во время развертывания. Это попытки получить доступ к ресурсу, который развертывается параллельно.

Ошибки обоих типов возвращают код ошибки, с помощью которого можно устранить неполадки развертывания. Ошибки обоих типов отображаются в [журнале действий](resource-group-audit.md). Однако ошибки проверки не отображаются в журнале развертывания, так как при их наличии развертывание не запускается.

### <a name="validation-errors"></a>Ошибки проверки

При развертывании через портал ошибка проверки появляется после отправки значений.

![Показ ошибки проверки через портал](./media/resource-manager-common-deployment-errors/validation-error.png)

Выберите сообщение для получения дополнительных сведений. На следующем изображении показана ошибка **InvalidTemplateDeployment** и сообщение, которое указывает, что развертывание заблокировано политикой.

![Отображение сведений о проверке](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Ошибки развертывания

Если операция прошла проверку, но завершилась ошибкой во время развертывания, вы увидите ошибку в уведомлениях. Выберите уведомление.

![уведомление об ошибке](./media/resource-manager-common-deployment-errors/notification.png)

Вы увидите больше сведений о развертывании. Щелкните область уведомления, чтобы получить дополнительные сведения об ошибке.

![сбой развертывания](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Вы увидите сообщение об ошибке и ее коды. Обратите внимание, что имеется два кода ошибки. Первый код ошибки (**DeploymentFailed**) является общей ошибкой, которая не содержит сведений, необходимых для ее устранения. Второй код ошибки (**StorageAccountNotFound**) предоставляет необходимые сведения. 

![Сведения об ошибке](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Включение ведения журнала отладки

Иногда требуются дополнительные сведения о запросе и ответе, чтобы узнать, что пошло не так. С помощью PowerShell или Azure CLI вы можете запросить, чтобы во время развертывания в журнал записывались дополнительные сведения.

- PowerShell

   В PowerShell для параметра **DeploymentDebugLogLevel** задайте значение All, ResponseContent или RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Проверьте содержимое запроса с помощью следующего командлета.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Или проверьте содержимое ответа, выполнив команду, указанную ниже.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Эти сведения помогут определить, правильно ли задано то или иное значение в шаблоне.

- Инфраструктура CLI Azure

   Для просмотра операций развертывания выполните следующую команду.

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Вложенный шаблон

   Чтобы вести журнал отладочной информации для вложенного шаблона, используйте элемент **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```

## <a name="create-a-troubleshooting-template"></a>Создание шаблона для устранения неполадок

В некоторых случаях устранить неполадки шаблона проще всего, проверяя его части. Можно создавать упрощенный шаблон, позволяющий сосредоточиться на части, которая, как вы считаете, вызывает ошибку. Для примера предположим, что вы получили сообщение об ошибке при указании ссылки на ресурс. Вместо того, чтобы проверять весь шаблон, создайте шаблон, возвращающий часть, которая может быть причиной проблемы. Это поможет определить, передаются ли правильные параметры, правильно ли используются функции шаблона и получается ли ожидаемый ресурс.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Или предположим, что возникают ошибки развертывания, которые, как вы считаете, связаны с неправильно заданными зависимостями. Проверьте шаблон, разбив его на более простые шаблоны. Сначала создайте шаблон, который развертывает только один ресурс (например, SQL Server). Когда вы убедитесь, что этот ресурс определен правильно, добавьте зависящий от него ресурс (например, базу данных SQL). Проверив правильность определения этих двух ресурсов, добавьте другие зависимые ресурсы (например, политики аудита). В перерывах между тестовыми развертываниями удаляйте группу ресурсов, чтобы гарантировать адекватную проверку зависимостей.


## <a name="next-steps"></a>Дополнительная информация
* Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](resource-group-audit.md).
* Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](resource-manager-deployment-operations.md).
