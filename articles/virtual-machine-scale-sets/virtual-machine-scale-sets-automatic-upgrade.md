---
title: Автоматические обновления ОС с масштабируемыми наборами виртуальных машин Azure | Документация Майкрософт
description: Узнайте, как выполнять автоматическое обновление операционной системы на экземплярах виртуальных машин в масштабируемом наборе
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 28a9b3d68037aac0c1198da4232c045487b01174
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30838229"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Автоматические обновления ОС масштабируемого набора виртуальных машин Azure

Автоматическое обновление образа ОС — это предварительная версия функции для масштабируемых наборов виртуальных машин Azure, которая автоматически обновляет все виртуальные машины до последней версии образа ОС.

Автоматическое обновление ОС имеет следующие характеристики:

- После настройки последний образ ОС, опубликованный издателем, автоматически применяется к масштабируемому набору без вмешательства пользователя.
- Пакеты экземпляров последовательно обновляются каждый раз, когда издатель публикует новый образ платформы.
- Интегрируется с проверкой работоспособности приложений (необязательно, но настоятельно рекомендуется для безопасности).
- Работает со всеми размерами виртуальных машин.
- Работает с образами платформ Windows и Linux.
- Автоматические обновления можно в любое время отключить (обновление ОС также можно инициировать вручную).
- Диск ОС виртуальной машины заменяется новым диском ОС с последней версией образа. Запускаются настраиваемые расширения и сценарии пользовательских данных, а сохраненные диски данных сохраняются.


## <a name="preview-notes"></a>Заметки к предварительной версии 
На этапе предварительной версии действуют следующие ограничения:

- Автоматические обновления ОС поддерживают только [четыре номера SKU ОС](#supported-os-images). Отсутствует соглашение об уровне обслуживания и гарантии. Мы не рекомендуем использовать автоматические обновления для критически важных рабочих нагрузок на этапе предварительной версии.
- Шифрование диска Azure (в настоящее время доступно в предварительной версии) **не** поддерживается в настоящий момент для автоматического обновления ОС масштабируемого набора виртуальных машин.


## <a name="register-to-use-automatic-os-upgrade"></a>Регистрация для использования автоматического обновления ОС
Чтобы использовать функцию автоматического обновления ОС, зарегистрируйте поставщик предварительной версии с помощью [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) следующим образом:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Примерно через 10 минут отобразится состояние *Зарегистрировано*. Текущее состояние регистрации можно проверить с помощью командлета [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). После регистрации убедитесь, что поставщик *Microsoft.Compute* зарегистрирован с помощью командлета [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) следующим образом:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

> [!NOTE]
> Кластеры Service Fabric имеют собственное понятие работоспособности приложения. Однако масштабируемые наборы без Service Fabric используют проверку работоспособности подсистемы балансировки нагрузки для мониторинга работоспособности приложения. Чтобы зарегистрировать функцию поставщика для проверки работоспособности, используйте командлет [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) следующим образом:
>
> ```powershell
> Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
> ```
>
> Примерно через 10 минут отобразится состояние *Зарегистрировано* (снова). Текущее состояние регистрации можно проверить с помощью командлета [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). После регистрации убедитесь, что поставщик *Microsoft.Network* зарегистрирован с помощью командлета [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) следующим образом:
>
> ```powershell
> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
> ```

## <a name="portal-experience"></a>Взаимодействие с порталом
После выполнения регистрации можете перейти на [портал Azure](https://aka.ms/managed-compute), чтобы включить автоматическое обновление ОС для масштабируемого набора и просмотреть ход обновлений.

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Поддерживаемые образы ОС
В настоящее время поддерживаются только определенные образы платформ ОС. Сейчас невозможно использовать самостоятельно созданные пользовательские образы. Для свойства *version* образа платформы следует установить значение *latest*.

Сейчас поддерживаются следующие номера SKU (в дальнейшем будут добавлены дополнительные):
    
| ИЗДАТЕЛЬ               | ПРЕДЛОЖЕНИЕ         |  Sku               | Version (версия)  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | последняя   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | последняя   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | последняя   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | последняя   |



## <a name="application-health-without-service-fabric"></a>Работоспособность приложения без Service Fabric
> [!NOTE]
> Этот раздел применим только для масштабируемых наборов без Service Fabric. В Service Fabric предусмотрено собственное представление о работоспособности приложения. При использовании автоматического обновления ОС с помощью Service Fabric домен обновления развертывает новый образ ОС, чтобы обеспечить высокий уровень доступности для служб, работающих в Service Fabric. Дополнительные сведения о характеристиках устойчивости кластера Service Fabric см. в [этой документации](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Во время обновления ОС экземпляры виртуальных машин в масштабируемом наборе обновляются по одному пакету. Обновление должно продолжаться, только если приложение клиента работоспособно на обновленных экземплярах виртуальных машин. По этой причине необходимо настроить приложение так, чтобы оно сообщало о работоспособности в модуль обновления ОС масштабируемого набора. При обновлении ОС платформа анализирует состояние энергопотребления виртуальной машины и состояние подготовки расширений, чтобы определить, является ли экземпляр виртуальной машины работоспособным после обновления. Во время обновления ОС экземпляра виртуальной машины диск ОС экземпляра заменяется новым диском на основе последней версии образа. После обновления ОС настроенные расширения запускаются на этих виртуальных машинах. Приложение считается работоспособным, только если все расширения на виртуальной машине успешно подготовлены. 

Кроме того, масштабируемый набор *нужно* настроить для проверок работоспособности приложений, с помощью которых платформа сможет получать точную информацию о текущем состоянии приложения. Проверки работоспособности приложения — это проверки пользовательского балансировщика нагрузки, которые используются в качестве сообщения о работоспособности. Приложение, работающее на экземпляре виртуальной машины из масштабируемого набора, может отвечать на внешние HTTP- или TCP-запросы, указывающие на его работоспособность. Дополнительные сведения о работе проверок пользовательского балансировщика нагрузки см. в статье [Проверки балансировщика нагрузки](../load-balancer/load-balancer-custom-probe-overview.md).

Если масштабируемый набор настроен для использования нескольких групп размещения, необходимо использовать проверки с [балансировщиком нагрузки уровня "Стандартный"](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

### <a name="important-keep-credentials-up-to-date"></a>Важно: учетные данные должны быть обновлены
Если ваш масштабируемый набор использует любые учетные данные для доступа к внешним ресурсам, например, если настроено расширение виртуальной машины, которое использует маркер SAS для учетной записи хранения, убедитесь, что учетные данные обновлены. Если срок действия всех учетных данных, в том числе сертификатов и маркеров, истек, обновление завершится сбоем, а первый пакет виртуальных машин останется в состоянии сбоя.

В случае сбоя аутентификации ресурса рекомендуем выполнить следующие шаги по восстановлению виртуальных машин и повторному включению автоматического обновления ОС.

* Повторно создайте маркер (или любые другие учетные данные), переданный в ваши расширения.
* Убедитесь, что все учетные данные, используемые внутри виртуальной машины для взаимодействия с внешними сущностями, обновлены.
* Обновите расширения в модели масштабируемого набора с новыми маркерами.
* Разверните обновленный масштабируемый набор, в котором будут обновлены все экземпляры виртуальных машин, включая сбойные. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Настройка пользовательской проверки балансировщика нагрузки в качестве проверки работоспособности приложения в масштабируемом наборе
*Необходимо* явно создать проверку подсистемы балансировки нагрузки на работоспособность масштабируемого набора. Вы можете использовать одну конечную точку для имеющейся проверки HTTP или TCP, но для проверки работоспособности может потребоваться подход, отличный от традиционного для проверки балансировщика нагрузки. Например, при традиционной проверке балансировщика нагрузки может вернуться сообщение о нерабочем состоянии, если нагрузка на экземпляр слишком высока. Такая проверка может оказаться непригодной для определения работоспособности экземпляра во время автоматического обновления ОС. Настройте для проверки высокую частоту (менее двух минут).

Проверка балансировщика нагрузки может быть указана в параметре *networkProfile* масштабируемого набора и может быть связана либо с внутренним, либо с общедоступным балансировщиком нагрузки следующим образом:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Принудительное применение политики обновления образа ОС в подписке
Для безопасного обновления настоятельно рекомендуется применить политику обновления. Эта политика может потребовать проверку работоспособности приложения в подписке. Следующая политика Azure Resource Manager отклоняет развертывания, для которых нет автоматических настроек обновления образа ОС:

1. Получите встроенное определение политики Azure Resource Manager с помощью командлета [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) следующим образом:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Назначьте политику подписке с помощью командлета [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) следующим образом:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Настройка автоматического обновления
Чтобы настроить автоматическое обновление, убедитесь, что свойство *automaticOSUpgrade* имеет значение *true* в определении модели масштабируемого набора. Это свойство можно настроить с помощью Azure PowerShell или Azure CLI 2.0.

В следующем примере используется Azure PowerShell (4.4.1 или более поздней версии) для настройки автоматического обновления масштабируемого набора *myVMSS* в группе ресурсов *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


В следующем примере используется Azure CLI (2.0.20 или более поздней версии) для настройки автоматического обновления масштабируемого набора *myVMSS* в группе ресурсов *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Проверка состояния автоматического обновления ОС
Вы можете проверить состояние последнего обновления ОС масштабируемого набора с помощью Azure PowerShell, Azure CLI 2.0 или API REST.

### <a name="azure-powershell"></a>Azure PowerShell
В следующем примере используется Azure PowerShell (4.4.1 или более поздней версии) для проверки состояния масштабируемого набора *myVMSS* в группе ресурсов *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
В следующем примере используется Azure CLI (2.0.20 или более поздней версии) для проверки состояния масштабируемого набора *myVMSS* в группе ресурсов *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>ИНТЕРФЕЙС REST API
В следующем примере используется REST API для проверки состояния масштабируемого набора *myVMSS* в группе ресурсов *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

Вызов GET возвращает подобные свойства:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Автоматическое обновление ОС
Чтобы расширить использование проверок работоспособности приложения, при обновлении ОС масштабируемого набора выполняются следующие шаги:

1. Если более 20 % экземпляров являются неработоспособными, остановите обновление. В противном случае продолжайте.
2. Определите следующий пакет экземпляров виртуальных машин для обновления. При этом пакет должен содержать максимум 20 % от общего количества экземпляров.
3. Обновите ОС следующей партии экземпляров виртуальных машин.
4. Если более 20 % обновленных экземпляров являются неработоспособными, остановите обновление. В противном случае продолжайте.
5. Для наборов масштабирования, которые не являются частью кластера Service Fabric, процесс обновления ожидает до 5 минут, пока проверки не сообщат о работоспособности, а затем сразу же переключается на следующий пакет. Для наборов масштабирования, которые являются частью кластера Service Fabric, процесс обновления ожидает 30 минут, прежде чем переключаться на следующий пакет.
6. Если остались экземпляры для обновления, перейдите к шагу 1 для следующего пакета. В противном случае обновление будет завершено.

Модуль обновления ОС масштабируемого набора проверяет общее состояние работоспособности экземпляров виртуальных машин перед обновлением каждого пакета. При обновлении пакета может быть выполнено другое параллельное плановое или внеплановое обслуживание в центрах обработки данных Azure, которое может повлиять на доступность ваших виртуальных машин. Следовательно, возможно, что временно более 20 % экземпляров могут быть недоступны. В таких случаях в конце обновления текущего пакета обновление масштабируемого набора прекращается.


## <a name="deploy-with-a-template"></a>Развертывание с помощью шаблона

Следующий шаблон можно использовать для развертывания масштабируемого набора, для которого используются <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>последовательные автоматические обновления Ubuntu 16.04-LTS</a>.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Дополнительная информация
Дополнительные примеры использования автоматических обновлений ОС с масштабируемыми наборами см. на странице [предварительной версии функций в репозитории GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
