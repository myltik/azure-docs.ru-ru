---
title: "Виртуальная машина с несколькими сетевыми картами (Azure CLI 1.0) | Документация Майкрософт"
description: "Назначение виртуальной машине нескольких IP-адресов с помощью Azure CLI 1.0 | Resource Manager"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.lasthandoff: 03/28/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Назначение виртуальным машинам нескольких IP-адресов с помощью Azure CLI 1.0

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

В этой статье описывается создание виртуальной машины с помощью модели развертывания Azure Resource Manager с использованием Azure CLI 1.0. Для ресурсов, созданных с помощью классической модели развертывания, нельзя назначить несколько IP-адресов. Дополнительные сведения о моделях развертывания Azure см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../resource-manager-deployment-model.md) (Развертывание с помощью Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Создание виртуальной машины с несколькими IP-адресами

Эту задачу можно выполнить с помощью Azure CLI 1.0 (в этой статье) или [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md). Вы можете создать пример виртуальной машины с несколькими IP-адресами, как описано в нашем сценарии. Измените имена переменных и типы IP-адресов в соответствии с требованиями этой реализации.

1. Установите и настройте Azure CLI 1.0, следуя инструкциям из [этой статьи](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), а затем войдите в учетную запись Azure, выполнив команду `azure-login`.

2. Создайте группу ресурсов:
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. Создайте виртуальную сеть:

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. Создайте подсеть в виртуальной сети:

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. Создайте учетную запись хранения для виртуальной машины. Перед выполнением следующей команды замените *mystorageaccount* на уникальное имя. Это имя должно быть уникальным в Azure.

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. Создайте конфигурации IP, сетевую карту и присвойте конфигурации IP сетевой карте. Конфигурации можно добавлять, удалять или изменять по мере необходимости. В сценарии описаны следующие конфигурации.

    **IPConfig-1**

    Выполните указанные ниже команды, чтобы создать:

    - ресурс общедоступного IP-адреса с общедоступным статическим IP-адресом;
    - сетевую карту, назначив ей общедоступный IP-адрес и статический частный IP-адрес.
    
    Замените *mypublicdns* именем, которое является уникальным в пределах расположения Azure.

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > За общедоступные IP-адреса взимается номинальная плата. Дополнительные сведения о ценах на IP-адреса см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses). Число общедоступных IP-адресов, которые можно использовать в одной подписке, ограничено. Сведения об ограничениях см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md#networking-limits).

    **IPConfig-2**

     Выполните следующие команды, чтобы создать ресурс общедоступного IP-адреса и IP-конфигурацию с общедоступным статическим IP-адресом и частным статическим IP-адресом.
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    Выполните следующие команды, чтобы создать конфигурацию IP с частным статическим IP-адресом без общедоступного IP-адреса:

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >Хотя в этом руководстве все IP-конфигурации назначаются одному сетевому адаптеру, вы также можете назначить несколько IP-конфигураций любому сетевому адаптеру, связанному с виртуальной машиной. Изучите статью о создании виртуальной машины с несколькими сетевыми адаптерами.

7. Создание виртуальной машины Linux 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    Например, чтобы использовать размер виртуальной машины Standard DS2 v2, просто добавьте свойство `--vm-size Standard_DS3_v2` в команду `azure vm create` на шаге 6.

8. Введите следующую команду, чтобы просмотреть свойства сетевого адаптера и связанных с ним IP-конфигураций:

    ```azurecli
    azure network nic show --resource-group $RgName    --name myNic1
    ```
9. Добавьте в операционную систему виртуальной машины частные IP-адреса, выполнив действия, соответствующие вашей операционной системе, как описано в разделе [Добавление IP-адресов в операционную систему виртуальной машины](#os-config) этой статьи.

## <a name="add"></a>Добавление IP-адресов в виртуальную машину

Выполнив следующие шаги, вы сможете назначить дополнительные частные или общедоступные IP-адреса существующим сетевым адаптерам. Примеры созданы на основе [сценария](#Scenario), описанного в этой статье.

1. Откройте интерфейс командной строки Azure и выполните в рамках одного сеанса командной строки все действия, описанные в этом разделе. Если интерфейс командной строки Azure еще не установлен, выполните инструкции из статьи [Установка Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), а затем войдите в учетную запись Azure.

2. Выполните действия, описанные в одном из следующих разделов, с учетом задач.

    - **Добавление частного IP-адреса**
    
        Чтобы назначить сетевому адаптеру частный IP-адрес, создайте IP-конфигурацию с помощью следующей команды. Статический адрес должен быть свободен в используемой подсети.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        Вы можете создать любое количество конфигураций, указывая для них уникальные имена и уникальные частные IP-адреса (если используются статические IP-адреса).

    - **Добавление общедоступного IP-адреса**
    
        Чтобы добавить общедоступный IP-адрес, его нужно связать с новой или имеющейся IP-конфигурацией. Выполните шаги, описанные в одном из следующих разделов, в соответствии с задачами.

        > [!NOTE]
        > За общедоступные IP-адреса взимается номинальная плата. Дополнительные сведения о ценах на IP-адреса см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses). Число общедоступных IP-адресов, которые можно использовать в одной подписке, ограничено. Сведения об ограничениях см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md#networking-limits).
        >

        **Связывание ресурса с новой IP-конфигурацией**
    
        Чтобы добавить общедоступный IP-адрес в новую IP-конфигурацию, необходимо добавить и частный IP-адрес, так как все IP-конфигурации должны иметь частный IP-адрес. В конфигурацию можно добавить имеющийся ресурс общедоступного IP-адреса или создать новый. Чтобы создать ресурс, выполните следующую команду:

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

         Чтобы создать новую конфигурацию IP с частным статическим IP-адресом и связанным ресурсом общедоступного IP-адреса *myPublicIP3*, введите следующую команду:

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **Связывание ресурса с существующей IP-конфигурацией**

        Ресурс общедоступного IP-адреса может быть связан только с такой IP-конфигурацией, у которой еще нет такого ресурса. Чтобы определить, связан ли с конкретной IP-конфигурацией какой-либо общедоступный IP-адрес, выполните следующую команду:

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        Найдите в возвращаемых данных строку, похожую на приведенную ниже для IPConfig-3.

        ```            
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        Столбец **Общедоступный IP-адрес** для IP-конфигурации *IpConfig-3* пуст. Это означает, что сейчас с этой конфигурацией общедоступные IP-адреса не связаны. Вы можете добавить к конфигурации IpConfig-3 имеющийся ресурс общедоступного IP-адреса или создать новый ресурс, выполнив следующую команду:

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        Чтобы связать ресурс общедоступного IP-адреса с имеющейся IP-конфигурацией с именем *IPConfig-3*, выполните следующую команду:
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. Просмотрите частные IP адреса и ресурсы общедоступных IP-адресов, назначенные сетевому адаптеру. Для этого введите следующую команду.

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      Выходные данные аналогичны приведенным ниже.
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. Добавьте в операционную систему виртуальной машины частные IP-адреса, которые вы ранее назначили сетевой карте. Для этого выполните инструкции из раздела [Добавление IP-адресов в операционную систему виртуальной машины](#os-config) этой статьи. Не добавляйте в операционную систему общедоступные IP-адреса.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

