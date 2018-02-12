---
title: "Настройка конечных точек служб виртуальной сети Azure | Документация Майкрософт"
description: "Узнайте, как включать и отключать конечные точки служб в виртуальной сети"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: e2242851d51dee56679231b9f34c8b474ba6578d
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="configure-virtual-network-service-endpoints"></a>Настройка конечных точек служб виртуальной сети

Конечные точки служб виртуальной сети обеспечивают защиту ресурсов служб Azure для виртуальной сети Azure, полностью исключая доступ к этим ресурсам через Интернет. Конечные точки служб обеспечивают прямое подключение к службе Azure из виртуальной сети, позволяя использовать частное пространство адресов вашей виртуальной сети для доступа к службам Azure. Трафик, поступающий в службы Azure через конечные точки служб, всегда остается в магистральной сети Microsoft Azure. См. дополнительные сведения о [конечных точках служб виртуальной сети](virtual-network-service-endpoints-overview.md).

В этой статье представлены инструкции по включению и отключению конечных точек служб. Если конечная точка службы Azure будет включена в подсети, вы можете защитить определенные ресурсы служб в виртуальной сети.

Конечные точки служб можно настроить с помощью [портала Azure](#azure-portal), [Azure PowerShell](#azure-powershell), [интерфейса командной строки Azure](#azure-cli) или [шаблона](#resource-manager-template) Azure Resource Manager.

>[!NOTE]
Во время действия предварительной версии компонент конечных точек служб виртуальной сети поддерживается в определенных регионах. Список поддерживаемых регионов см. на странице с [обновлениями для виртуальной сети Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Общие сведения о настройке конечной точки службы

- Конечные точки служб можно настроить только в виртуальных сетях, развернутых с помощью модели Azure Resource Manager.

- Конечные точки служб задаются в каждой подсети виртуальной сети.

- В подсети можно настроить только одну конечную точку для службы. Можно настроить несколько конечных точек для разных служб (например, для службы хранилища Azure и Azure SQL).

- Вы можете включить конечные точки в новой или существующей подсети.

- Расположение для конечной точки настраивается автоматически. По умолчанию конечные точки служб настроены для региона виртуальной сети. Чтобы служба хранилища Azure поддерживала сценарии региональной отработки отказа, конечные точки автоматически настраиваются для [пар регионов Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

  >[!NOTE]
  В зависимости от размера виртуальной сети и подсети включение конечной точки службы может занять некоторое время. Убедитесь, что при включении конечных точек служб не выполняются критически важные задачи. Коммутатор конечных точек направляет трафик на каждый сетевой интерфейс в подсети и может закрыть все открытые подключения TCP. 

- Вызов конечной точки службы определяется как успешный, когда трафик, поступающий в службу на все сетевые интерфейсы в подсети, переключается на частные IP-адреса виртуальной сети.

- Действующие маршруты для проверки настройки конечной точки.

   Чтобы проверить правильность настройки конечной точки службы, в список действующих маршрутов для любого сетевого интерфейса в подсети добавляется маршрут по умолчанию со значением nextHopType, равным VirtualNetworkServiceEndpoint, для каждой службы в каждом регионе. Ознакомьтесь со сведениями об [устранении неполадок с действующими маршрутами](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow).

   >[!NOTE]
   Действующие маршруты можно просмотреть только в том случае, если один или несколько сетевых интерфейсов (NIC) настроены и связаны с работающей виртуальной машиной в подсети.

## <a name="azure-portal"></a>Портал Azure

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Настройка конечной точки службы в подсети во время создания виртуальной сети

1. Откройте [портал Azure](https://portal.azure.com/).
Войдите в Azure с помощью учетной записи Azure. Если у вас нет учетной записи Azure, вы можете зарегистрироваться и получить бесплатную пробную версию. Учетная запись должна предоставлять необходимые [разрешения](#provisioning) для создания виртуальной сети и конечной точки службы.
2. Последовательно выберите "+Создать" > "Сеть" > "Виртуальная сеть" > "+Добавить".
3. В окне "Создание виртуальной сети" введите приведенные ниже значения, а затем щелкните "Создать":

Параметр | Значение
------- | -----
ИМЯ    | myVnet
Пространство адресов | 10.0.0.0/16
Имя подсети|mySubnet
Диапазон адресов подсети|10.0.0.0/24
Группа ресурсов|Оставьте выбранным пункт "Создать" и введите имя.
Расположение|Любой поддерживаемый регион, например восточная Австралия
Подписка|Выберите свою подписку.
__ServiceEndpoints__|Включено
__Службы__ | Выберите одну или все доступные службы. Поддерживаются следующие службы: __Microsoft.Storage, Microsoft.Sql__.

Выберите службы для создания конечных точек: ![Выбор служб для создания конечных точек](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Проверьте правильность всех параметров и нажмите кнопку "Создать".

![Установка конечной точки службы](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Чтобы завершить защиту ресурсов служб Azure в виртуальной сети, ознакомьтесь с документацией службы в разделе [Дальнейшие действия](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Проверка конфигурации конечной точки службы

Убедитесь, что конечные точки служб настроены, выполнив следующие действия:

- В разделе ресурсов щелкните "Виртуальные сети". Найдите виртуальную сеть.
- Щелкните имя виртуальной сети и перейдите к пункту "Конечные точки служб".
- Для настроенных конечных точек отображается значение "Успешно". Также отображаются автоматически настроенные расположения.

![Проверка настройки конечных точек службы](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Действующие маршруты для проверки настройки конечных точек

Чтобы просмотреть действующие маршруты для сетевого интерфейса (NIC) в подсети, щелкните в ней любой NIC. В разделе "Поддержка и устранение неполадок" щелкните Effective routes (Действующие маршруты). Если конечная точка настроена, отобразится новый маршрут по умолчанию с префиксами адресов службы в качестве назначения. Для nextHopType будет установлено значение VirtualNetworkServiceEndpoint.

![Действующие маршруты для конечных точек служб](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Настройка конечных точек служб для существующих подсетей в виртуальной сети

1. В разделе ресурсов щелкните "Виртуальные сети" и найдите любую существующую виртуальную сеть.
2. Щелкните имя виртуальной сети и перейдите к пункту "Конечные точки служб".
3. Нажмите кнопку "Добавить". Выберите пункт "Служба". За один раз можно создать конечную точку только для одной службы. 
4. Выберите все подсети, в которых нужно применить конечную точку. Нажмите кнопку "Добавить".

![Настройка конечной точки службы для подсети](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Удаление конечных точек служб
1. В разделе ресурсов щелкните "Виртуальные сети". Найдите существующую виртуальную сеть, отфильтровав список по ее имени.
2. Щелкните имя виртуальной сети и перейдите к пункту "Конечные точки служб".
3. Выберите имя службы и щелкните правой кнопкой мыши запись конечной точки службы.
4. Нажмите кнопку "Удалить".

![Удаление конечной точки службы](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

Предварительные требования для настройки:

- Установите последнюю версию модуля [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) PowerShell. Если вы еще не работали с Azure PowerShell, ознакомьтесь со статьей [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Общие сведения об Azure PowerShell).
- Чтобы запустить сеанс PowerShell, нажмите кнопку **Пуск**, введите **powershell**, а затем щелкните **PowerShell**.
- В PowerShell войдите в Azure, введя команду `login-azurermaccount`. Учетная запись должна предоставлять необходимые [разрешения](#provisioning) для создания виртуальной сети и конечной точки службы.

### <a name="get-available-service-endpoints-for-azure-region"></a>Получение доступных конечных точек служб для региона Azure

Используйте команду ниже, чтобы получить список служб, которые поддерживаются для конечных точек для региона Azure.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Выходные данные: 
ИМЯ | ИД | type
-----|----|-------
Microsoft.Storage;|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Добавление конечной точки службы хранилища Azure в подсеть *mySubnet* при создании виртуальной сети *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Вы можете включить несколько служб, разделив их имена запятыми.
Пример: Microsoft.Storage, Microsoft.Sql.

Ожидаемые выходные данные:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Чтобы завершить защиту ресурсов служб Azure в виртуальной сети, ознакомьтесь с документацией службы в разделе [Дальнейшие действия](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Добавление нескольких конечных точек служб в существующую подсеть

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Ожидаемые выходные данные: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Просмотр конечных точек службы, которые настроены в подсети

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Выходные данные:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Удаление конечных точек службы в подсети
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Предварительные требования для настройки:
- Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. Дополнительные сведения о входе в систему см. в статье [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) (Приступая к работе с Azure CLI 2.0).
 - Учетная запись должна предоставлять необходимые [разрешения](#provisioning) для создания виртуальной сети и конечной точки службы.

 См. [полный список команд для виртуальной сети Azure CLI](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest).

### <a name="get-available-service-endpoints-for-azure-region"></a>Получение доступных конечных точек служб для региона Azure

Используйте команду ниже, чтобы получить список служб, которые поддерживаются для конечных точек в регионе Azure, например EastUS.
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Выходные данные:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Добавление конечной точки службы хранилища Azure в подсеть *mySubnet* при создании виртуальной сети *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Чтобы добавить несколько конечных точек: --service-endpoints Microsoft.Storage Microsoft.Sql.

Выходные данные:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Чтобы завершить защиту ресурсов служб Azure в виртуальной сети, ознакомьтесь с документацией службы в разделе [Дальнейшие действия](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Добавление нескольких конечных точек служб в существующую подсеть

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Ожидаемые выходные данные:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Просмотр конечных точек службы, которые настроены в подсети

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Удаление конечных точек службы в подсети
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Выходные данные: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Шаблон Resource Manager

### <a name="securing-azure-service-resources-to-vnets"></a>Защита ресурсов служб Azure в виртуальных сетях

Можно защитить отдельные ресурсы Azure в виртуальной сети при помощи конечных точек служб.

Загрузите пример [шаблона Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) для защиты учетной записи хранения в подсети виртуальной сети.

Этот шаблон создает виртуальную сеть с 2 подсетями и виртуальную машину с NIC в каждой подсети. Он включает конечную точку в одной подсети и обеспечивает в ней защиту учетной записи хранения.

Вы можете загрузить шаблон и изменить его части согласно сценарию.

Вместе с шаблоном предоставляются инструкции по его развертыванию с помощью портала Azure, PowerShell или Azure CLI. Убедитесь в наличии необходимых [разрешений](#provisioning) для настройки конечной точки и защиты учетной записи.

Для защиты ресурсов Azure в подсети:

- конечная точка службы должна быть настроена в этой подсети;
- ресурс должен быть защищен в виртуальной сети при помощи правила виртуальной сети для ресурса.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Удаление конечных точек служб с ресурсами, защищенными в подсети
Если ресурсы служб Azure защищены в подсети, при удалении конечной точки службы ресурс больше не будет доступен из подсети.
Повторного включения конечной точки недостаточно, чтобы восстановить доступ к ресурсам, которые ранее были защищены в подсети.

Чтобы повторно защитить ресурс службы в этой подсети, необходимо:

- снова включить конечную точку;
- удалить старое правило виртуальной сети для ресурса;
- добавить новое правило для защиты ресурса в подсети.

## <a name="provisioning"></a>Подготовка

Пользователь с правами на запись в виртуальной сети может настроить конечные точки служб в виртуальных сетях независимо друг от друга.

Для защиты ресурсов служб Azure в виртуальной сети пользователь должен иметь разрешение Microsoft.Network/JoinServicetoaSubnet для добавляемых подсетей. Это разрешение по умолчанию включено во встроенные роли администраторов служб и может быть изменено при создании настраиваемых ролей.

Узнайте больше о [встроенных ролях](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) и назначении разрешений, определенных для [настраиваемых ролей](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Виртуальные сети и ресурсы служб Azure могут находиться в одной или разных подписках. Если они находятся в разных подписках, ресурсы должны быть размещены в одном клиенте Active Directory (AD).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные инструкции по защите ресурса службы в виртуальных сетях см. по следующим ссылкам:

[Защита учетных записей хранения Azure в виртуальных сетях](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Защита Azure SQL в виртуальных сетях](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
