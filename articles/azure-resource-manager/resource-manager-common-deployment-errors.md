---
title: "Устранение распространенных ошибок развертывания в Azure | Документация Майкрософт"
description: "Описывается устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "ошибка развертывания, развертывание Azure, развернуть в Azure"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 54db95fd50e5d61d54d0c03a8589cfe8292c012c
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager | Microsoft Azure
В этой статье объясняется, как устранить некоторые распространенные ошибки при развертывании в Azure.

В этом разделе описаны следующие коды ошибок:

* [AccountNameInvalid](#accountnameinvalid);
* [ошибка авторизации](#authorization-failed).
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed);
* [DisallowedOperation](#disallowedoperation)
* [InvalidContentLink](#invalidcontentlink);
* [InvalidTemplate](#invalidtemplate);
* [MissingSubscriptionRegistration](#noregisteredproviderfound);
* [NotFound](#notfound);
* [NoRegisteredProviderFound](#noregisteredproviderfound);
* [OperationNotAllowed](#quotaexceeded);
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded);
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy);
* [ResourceNotFound](#notfound);
* [SkuNotAvailable](#skunotavailable).
* [StorageAccountAlreadyExists](#storagenamenotunique);
* [StorageAccountAlreadyTaken](#storagenamenotunique).

## <a name="deploymentfailed"></a>DeploymentFailed

Этот код ошибки указывает на общую ошибку развертывания, но не следует сразу же использовать его для устранения неполадок. Код ошибки, который действительно поможет устранить возникшую проблему, обычно находится на один уровень ниже этой ошибки. Например, на следующем рисунке показан код ошибки **RequestDisallowedByPolicy**, который находится под кодом ошибки развертывания.

![Отображение кода ошибки](./media/resource-manager-common-deployment-errors/error-code.png)

## <a name="skunotavailable"></a>SkuNotAvailable

При развертывании ресурса (как правило, виртуальной машины) могут появиться код ошибки и сообщение об ошибке:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Эта ошибка возникает, когда выбранный номер SKU ресурса (например, размер виртуальной машины) недоступен для указанного расположения. Чтобы устранить эту проблему, необходимо определить номера SKU, доступные в регионе. Для поиска доступных номеров SKU можно использовать PowerShell, портал или операцию REST.

- Для PowerShell используйте [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) и фильтрацию по расположению. Эта команда поддерживается только в Azure PowerShell последней версии.

  ```powershell
  Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
  ```

  Результаты включают список номеров SKU для расположения и имеющиеся ограничения для этого номера SKU.

  ```powershell
  ResourceType                Name      Locations Restriction                      Capability Value
  ------------                ----      --------- -----------                      ---------- -----
  availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
  availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
  virtualMachines      Standard_A0 southcentralus
  virtualMachines      Standard_A1 southcentralus
  virtualMachines      Standard_A2 southcentralus
  ```

- Для Azure CLI используется команда `az vm list-skus`. Для фильтрации выходных данных можно использовать `grep` или другую подобную служебную программу.

  ```
  az vm list-skus --output table
  ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
  ----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
  availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
  avilabilitySets  eastus              Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
  ```

- Чтобы сделать это с помощью [портала](https://portal.azure.com), войдите на него и добавьте ресурс с помощью пользовательского интерфейса. При настройке значений вы увидите доступные SKU для этого ресурса. Не нужно завершать развертывание.

    ![Доступные номера SKU](./media/resource-manager-common-deployment-errors/view-sku.png)

- Чтобы использовать REST API для виртуальных машин, отправьте следующий запрос.

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  Он возвращает доступные номера SKU и регионы в приведенном ниже формате.

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

Если вам не удалось найти подходящий номер SKU в этом или любом другом регионе, который соответствует потребностям вашей компании, обратитесь в [службу поддержки Azure](https://aka.ms/skurestriction).

## <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

Если возникла эта ошибка, значит, вы используете подписку, которой запрещен доступ к любым службам Azure, кроме Azure Active Directory. Подписка такого типа может использоваться, когда требуется доступ к классическому порталу, но запрещено развертывание ресурсов. Чтобы устранить эту проблему, необходимо использовать подписку, которая имеет разрешение на развертывание ресурсов.  

Чтобы просмотреть доступные подписки с помощью PowerShell, используйте следующую команду.

```powershell
Get-AzureRmSubscription
```

Чтобы выбрать текущую подписку, введите приведенную ниже команду.

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

Чтобы просмотреть доступные подписки с помощью Azure CLI 2.0, используйте следующую команду.

```azurecli
az account list
```

Чтобы выбрать текущую подписку, введите приведенную ниже команду.

```azurecli
az account set --subscription {subscription-name}
```

## <a name="invalidtemplate"></a>InvalidTemplate
Эта ошибка может появится в результате ошибок нескольких различных типов.

- Синтаксическая ошибка

   Если появляется сообщение об ошибке, указывающее, что шаблону не удалось пройти аутентификацию, возможно, в нем есть синтаксическая ошибка.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   Такую ошибку легко допустить, так как выражения шаблонов могут быть сложными. Например, представленное ниже присвоение имени для учетной записи хранения содержит один набор квадратных скобок, три функции, три набора круглых скобок, один набор одинарных кавычек и одно свойство:

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   Если вы не укажете соответствующий синтаксис, шаблон создаст значение, которое не будет соответствовать ожидаемому значению.

   При получении сообщения об ошибке такого типа тщательно проверьте синтаксис выражения. Рекомендуется использовать редактор JSON, например [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) или [Visual Studio Code](resource-manager-vs-code.md), в котором отображаются предупреждения о синтаксических ошибках.

- Неправильная длина сегментов

   Шаблон также считается недопустимым, если имя ресурса указано в неправильном формате.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   Имя ресурса на корневом уровне должно содержать на один сегмент меньше, чем тип ресурса. Каждый сегмент разделяется косой чертой. В следующем примере тип содержит два сегмента, а имя — один. Так что это **допустимое имя**.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   В приведенном ниже примере указано **недопустимое имя** , так как оно содержит такое же количество сегментов, что и тип.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   В случае дочерних ресурсов тип и имя должны содержать одинаковое количество сегментов. Это целесообразно, так как полное имя и тип дочернего элемента включает имя родительского элемента и его тип. Таким образом в полном имени на один сегмент меньше, чем в полном типе.

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   Разобраться с правильной длиной сегментов сложно при использовании типов Resource Manager, которые применяются для поставщиков ресурсов. Например, для применения блокировки ресурсов на веб-сайте требуется тип с четырьмя сегментами. Поэтому имя содержит три сегмента:

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- Копирование индекса не ожидается

   Ошибка **InvalidTemplate** возникает при применении элемента **copy** к части шаблона, которая не поддерживает этот элемент. Элемент copy можно применять только к типу ресурса. Нельзя применять элемент copy к свойству в типе ресурса. Например, его можно применить к виртуальной машине, но не к дискам операционной системы виртуальной машины. В некоторых случаях можно преобразовывать дочерние ресурсы в родительские для создания цикла копирования. Дополнительные сведения об использовании элемента copy см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

- Недопустимый параметр

   Если шаблон указывает допустимые значения для параметра, а вы укажете значение, отличное от одного из указанных, появится сообщение об ошибке, аналогичное следующему:

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   Внимательно проверьте допустимые значения в шаблоне и укажите одно из них во время развертывания.

- Обнаружена циклическая зависимость

   Эта ошибка возникает, когда ресурсы зависят друг от друга таким образом, что это не позволяет начать развертывание. Сочетание взаимозависимостей вынуждает два или более ресурсов ожидать другие ресурсы, которые также находятся в ожидании. Например, resource1 зависит от resource3, resource2 зависит от resource1, а resource3 зависит от resource2. Как правило, эту проблему можно устранить, удалив ненужные зависимости. 

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound и ResourceNotFound
Если шаблон содержит имя ресурса, которое не удается разрешить, появится сообщение об ошибке, аналогичное следующему:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

При попытке развернуть отсутствующий ресурс в шаблоне проверьте, нужно ли добавить зависимость. Resource Manager оптимизирует развертывание, создавая ресурсы параллельно, когда это возможно. Если один ресурс необходимо развернуть после другого, создайте зависимость от другого ресурса, используя в шаблоне элемент **dependsOn** . Например, при развертывании веб-приложения вам нужно создать план службы приложений. Если вы не указали, что веб-приложение зависит от плана службы приложений, Resource Manager создаст оба ресурса одновременно. При попытке указать свойство веб-приложения вы получите сообщение о том, что невозможно найти ресурс плана службы приложений, так как он еще не существует. Чтобы предотвратить эту ошибку, в веб-приложении следует настроить зависимость.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Предложения по устранению ошибок зависимостей доступны в разделе [Проверка последовательности развертывания](#check-deployment-sequence).

Эта ошибка также возникает, если ресурс существует в группе ресурсов, отличной от той, в которую выполняется развертывание. В этом случае используйте [функцию resourceId](resource-group-template-functions-resource.md#resourceid), чтобы получить полное имя ресурса.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

При попытке использовать функции [reference](resource-group-template-functions-resource.md#reference) или [listKeys](resource-group-template-functions-resource.md#listkeys) с ресурсом, который не удается разрешить, появится следующая ошибка:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

Найдите выражение с функцией **reference**. Убедитесь, что значения параметров правильные.

## <a name="parentresourcenotfound"></a>ParentResourceNotFound

Если один ресурс является родительским для другого ресурса, то перед созданием этого дочернего ресурса должен существовать его родительский ресурс. Если он еще не существует, появляется приведенная ниже ошибка.

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

Имя дочернего ресурса включает в себя имя родительского ресурса. Например, база данных SQL может быть определена следующим образом.

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Однако, если не задать зависимость от родительского ресурса, то дочерний ресурс может быть развернут раньше родительского. Чтобы устранить эту ошибку, добавьте соответствующую зависимость.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />

## <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists и StorageAccountAlreadyTaken
Для учетных записей хранения необходимо указывать имя ресурса, уникальное в среде Azure. Если не указать уникальное имя, возникнет такая ошибка:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Уникальное имя можно создать, используя соглашение об именовании и результат функции [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Если при развертывании учетной записи хранения указать для нее то же имя, что и для существующей учетной записи хранения в подписке, но указать другое расположение, появится сообщение о том, что учетная запись хранения уже существует в другом расположении. Удалите существующую учетную запись хранения или укажите то же расположение, что и для существующей учетной записи хранения.

## <a name="accountnameinvalid"></a>AccountNameInvalid
Ошибка **AccountNameInvalid** отображается при попытке указать для учетной записи хранения имя, содержащее запрещенные знаки. Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и букв нижнего регистра. Функция [UniqueString](resource-group-template-functions-string.md#uniquestring) возвращает 13 знаков. Если используется сцепка префикса с результатом **uniqueString**, то следует использовать префикс, содержащий не более 11 знаков.

## <a name="badrequest"></a>BadRequest

При указании недопустимого значения для свойства может возникнуть состояние BadRequest. Например, если указано неправильное значение SKU для учетной записи хранения, развертывание завершится сбоем. Чтобы определить допустимые значения для свойства, просмотрите сведения о [REST API](/rest/api) для типа развертываемого ресурса.

<a id="noregisteredproviderfound" />

## <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound и MissingSubscriptionRegistration
При развертывании ресурсов вы можете получить следующий код ошибки и сообщение об ошибке:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Или может появиться похожее сообщение.

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Эти ошибки возникают по одной из следующих причин:

1. Для подписки не зарегистрирован поставщик ресурсов.
2. Версия API не поддерживается для выбранного типа ресурса.
3. Расположение не поддерживается для выбранного типа ресурса.

В сообщении об ошибке должны быть указаны поддерживаемые расположения и версии API. Вы можете изменить шаблон, используя одно из предложенных значений. Большинство поставщиков, но не все, регистрируются автоматически порталом Azure или интерфейсом командной строки, который вы используете. Если ранее вы не использовали конкретный поставщик ресурсов, возможно, потребуется зарегистрировать такой поставщик. Дополнительные сведения о поставщиках ресурсов можно получить с помощью PowerShell или интерфейса командной строки Azure.

**Портал**

Просмотреть состояние регистрации и зарегистрировать пространство имен поставщика ресурсов можно на портале.

1. Для своей подписки выберите **Поставщики ресурсов**.

   ![Выбор поставщиков ресурсов](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. Просмотрите список поставщиков ресурсов и, при необходимости, щелкните ссылку **Зарегистрировать**, чтобы зарегистрировать поставщик ресурсов типа, который вы пытаетесь развернуть.

   ![список поставщиков ресурсов](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

Чтобы просмотреть состояние регистрации, используйте командлет **Get-AzureRmResourceProvider**.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Чтобы зарегистрировать поставщик, используйте командлет **Register-AzureRmResourceProvider** и укажите имя поставщика ресурсов, который необходимо зарегистрировать.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Чтобы получить поддерживаемые расположения для определенного типа ресурсов, используйте следующую команду:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Чтобы получить поддерживаемые версии API для определенного типа ресурсов, используйте следующую команду:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**Интерфейс командной строки Azure**

Чтобы узнать, зарегистрирован ли поставщик, используйте команду `azure provider list` .

```azurecli
az provider list
```

Чтобы зарегистрировать поставщик ресурсов, используйте команду `azure provider register` и укажите *пространство имен* , которое следует зарегистрировать.

```azurecli
az provider register --namespace Microsoft.Cdn
```

Чтобы просмотреть поддерживаемые расположения и версии API для типа ресурса, используйте следующую команду.

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />

## <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded и OperationNotAllowed
Если развертывание превышает квоту, могут возникнуть проблемы, связанные с группой ресурсов, подписками, учетными записями и другими компонентами. Например, для подписки может быть настроено ограничение числа ядер для региона. При попытке развертывания виртуальной машины с большим количеством ядер, чем разрешено, вы получите сообщение о том, что квота превышена.
Дополнительные сведения о квотах Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).

Проверить квоты ядер в своей подписке можно с помощью команды `azure vm list-usage` в интерфейсе командной строки Azure. В примере ниже показано, что квота ядер для бесплатной пробной учетной записи равна 4:

```azurecli
az vm list-usage --location "South Central US"
```

Возвращаемые данные:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

При развертывании шаблона, создающего более четырех ядер в западной части США, возникнет ошибка развертывания, аналогичная следующей:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Можно также использовать командлет PowerShell **Get-AzureRmVMUsage** .

```powershell
Get-AzureRmVMUsage
```

Возвращаемые данные:

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

В таких случаях следует перейти на портал и зарегистрировать проблему в службе поддержки, чтобы поднять свою квоту для региона, в котором требуется осуществить развертывание.

> [!NOTE]
> Следует помнить, что для групп ресурсов квоты устанавливаются для каждого отдельного региона, а не для всей подписки. Если необходимо развернуть 30 ядер в западной части США, необходимо запросить 30 ядер управления ресурсами в этом регионе. Если необходимо развернуть 30 ядер в любом из регионов, к которым у вас есть доступ, следует запросить 30 ядер Resource Manager во всех регионах.
>
>

## <a name="invalidcontentlink"></a>InvalidContentLink
При появлении ошибки

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

скорее всего была предпринята попытка связать недоступный вложенный шаблон. Внимательно проверьте URI, указанный для вложенного шаблона. Если шаблон существует в учетной записи хранения, убедитесь, что URI доступен. Возможно, понадобится передать маркер SAS. Дополнительные сведения см. в статье [Использование связанных шаблонов в диспетчере ресурсов Azure](resource-group-linked-templates.md).

## <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Эта ошибка возникает, когда подписка включает в себя политику ресурсов, предотвращающую действие, которое вы пытаетесь выполнить во время развертывания. В сообщении об ошибке найдите идентификатор политики.

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

В **PowerShell** укажите идентификатор политики как параметр **Id**, чтобы получить сведения о политике, заблокировавшей развертывание.

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

В **Azure CLI** укажите имя определения политики.

```azurecli
az policy definition show --name regionPolicyAssignment
```

Дополнительные сведения см. в следующих статьях:

- [Ошибка RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md).
- [Общие сведения о политике ресурсов](resource-manager-policy.md).

## <a name="authorization-failed"></a>Ошибка авторизации
Эта ошибка может возникнуть во время развертывания, если учетная запись или субъект-служба, пытающиеся развернуть ресурсы, не имеют доступа на выполнение этих действий. Azure Active Directory позволяет вам или вашему администратору управлять удостоверениями, которые могут получать доступ к тем или иным ресурсам. Например, если учетной записи назначена роль "Читатель", вы не сможете создавать ресурсы. В таком случае появится сообщение об ошибке авторизации.

Дополнительные сведения об управлении доступом на основе ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../active-directory/role-based-access-control-configure.md).


## <a name="next-steps"></a>Дальнейшие действия
* Сведения о действиях аудита см. в статье [Операции аудита с помощью Resource Manager](resource-group-audit.md).
* Дополнительные сведения об определении ошибок во время развертывания см. в статье [Просмотр операций развертывания с помощью портала Azure](resource-manager-deployment-operations.md).

