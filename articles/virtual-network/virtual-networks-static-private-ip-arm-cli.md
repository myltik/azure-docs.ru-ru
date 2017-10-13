---
title: "Настройка частных IP-адресов для виртуальных машин (Azure CLI 2.0) | Документация Майкрософт"
description: "Узнайте, как настроить частные IP-адреса для виртуальных машин с помощью интерфейса командной строки Azure (CLI) версии 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 071156367c1f819a00d31f1d0335e301391fda81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-20"></a>Настройка частных IP-адресов для виртуальной машины с помощью Azure CLI 2.0

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи 

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки. 

- [Azure CLI 1.0](virtual-networks-static-private-ip-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager. 
- [Azure CLI 2.0](#specify-a-static-private-ip-address-when-creating-a-vm) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager (описывается в этой статье).

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье описывается модель развертывания с использованием менеджера ресурсов. Кроме того, вы можете [управлять статическим частным IP-адресом в классической модели развертывания](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Для выполнения приведенных ниже примеров команд Azure CLI 2.0 требуется созданная простая среда. Чтобы выполнять команды в соответствии с указаниями, представленными в этом документе, сначала постройте тестовую среду, как описано в статье [Создание виртуальной сети](virtual-networks-create-vnet-arm-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Указание статического частного IP-адреса при создании виртуальной машины

Чтобы создать виртуальную машину с именем *DNS01* в подсети *FrontEnd* виртуальной сети *TestVNet* со статическим частным IP-адресом *192.168.1.101*, выполните следующие действия.

1. Установите и настройте последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) (если вы еще этого не сделали), а затем войдите с использованием учетной записи Azure, выполнив команду [az login](/cli/azure/#login). 

2. Создайте общедоступный IP-адрес для виртуальной машины с помощью команды [az network public-ip create](/cli/azure/network/public-ip#create). В списке, который откроется после выполнения команды, будут указаны используемые параметры.

    > [!NOTE]
    > Возможно, вам понадобится использовать разные значения для аргументов на этих и последующих шагах в зависимости от среды.
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Ожидаемые выходные данные:
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group` — имя группы ресурсов, в которой будет создан общедоступный IP-адрес.
   * `--name` —имя общедоступного IP-адреса.
   * `--location` — регион Azure, в котором будет создан общедоступный IP-адрес.

3. Выполните команду [az network nic create](/cli/azure/network/nic#create), чтобы создать сетевую карту со статическим частным IP-адресом. В списке, который откроется после выполнения команды, будут указаны используемые параметры. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Ожидаемые выходные данные:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Параметры

    * `--private-ip-address`: статический частный IP-адрес для сетевой карты.
    * `--vnet-name`: имя виртуальной сети для создания сетевой карты.
    * `--subnet`: имя подсети для создания сетевой карты.

4. Выполните команду [azure vm create](/cli/azure/vm/nic#create) , чтобы создать виртуальную машину с использованием общедоступного IP-адреса и сетевой карты, созданных ранее. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Ожидаемые выходные данные:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Параметры, отличные от базовых параметров [az vm create](/cli/azure/vm#create).

   * `--nics` — имя сетевой карты, к которой подключена виртуальная машина.
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Получение сведений о статическом частном IP-адресе виртуальной машины

Чтобы просмотреть сведения о созданном статическом частном IP-адресе, выполните следующую команду Azure CLI. Обратите внимание на значения *метода выделения частных IP-адресов* и *частного IP-адреса*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Ожидаемые выходные данные:

```json
"192.168.1.101"
```

Чтобы отобразить определенные сведения об IP-адресе сетевой карты для этой виртуальной машины, отправьте запрос к сетевой карте:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Результат будет выглядеть следующим образом.

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Удаление статического частного IP-адреса виртуальной машины

Статический частный IP-адрес нельзя удалить из сетевой карты с использованием Azure CLI для развертываний Resource Manager. Необходимо следующее:
- Создайте сетевую карту, использующую динамический IP-адрес.
- Настройте созданную сетевую карту на виртуальной машине. 

Чтобы изменить сетевую карту для виртуальной машины, которая использовалась в приведенных выше командах, сделайте следующее.

1. Выполните команду **azure network nic create**, чтобы создать сетевую карту с новым IP-адресом путем выделения динамических IP-адресов. Обратите внимание, что так как IP-адрес не указан, используется **динамический** способ выделения.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Ожидаемые выходные данные:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Выполните команду **azure vm set** , чтобы изменить сетевую карту, используемую виртуальной машиной.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    Ожидаемые выходные данные:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Если размер виртуальной машины позволяет подключить несколько сетевых карт, выполните команду **azure network nic delete**, чтобы удалить старую сетевую карту.
   
## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с информацией о [зарезервированных общедоступных IP-адресах](virtual-networks-reserved-public-ip.md) .
* Узнайте об [общедоступных IP-адресах уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Ознакомьтесь с информацией о [REST API зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx).

