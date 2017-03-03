---
title: "Назначение виртуальным машинам Azure нескольких IP-адресов с помощью Azure CLI | Документация Майкрософт"
description: "Узнайте, как назначить виртуальной машине несколько IP-адресов с использованием интерфейса командной строки Azure."
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
ms.sourcegitcommit: 7e99731f6826e563109da734a80fcccfff85676a
ms.openlocfilehash: 3deb0e1668aae15a7d9abe00791f4524ed49d77b
ms.lasthandoff: 02/21/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Назначение виртуальным машинам нескольких IP-адресов с помощью интерфейса командной строки Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

В этой статье описывается создание виртуальной машины с помощью модели развертывания Azure Resource Manager с использованием Azure CLI. Для ресурсов, созданных с помощью классической модели развертывания, нельзя назначить несколько IP-адресов. Дополнительные сведения о моделях развертывания Azure см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../resource-manager-deployment-model.md) (Развертывание с помощью Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Создание виртуальной машины с несколькими IP-адресами

Вы можете создать пример виртуальной машины с несколькими IP-адресами, как описано в нашем сценарии. Измените имена переменных и типы IP-адресов в соответствии с требованиями этой реализации.

1. Установите и настройте интерфейс командной строки Azure, следуя инструкциям в [этой статье](../xplat-cli-install.md), а затем войдите в учетную запись Azure.

2. Зарегистрируйтесь для получения предварительной версии, выполнив следующие команды в PowerShell после входа, и выберите соответствующую подписку:
    ```
    Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

    Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
    Не пытайтесь выполнить остальные шаги, пока не увидите следующий результат при выполнении команды ```Get-AzureRmProviderFeature```:
        
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------      
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >Это может занять несколько минут.

3. [Создайте группу ресурсов](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations), а затем [виртуальную сеть и подсеть](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Измените значения ``` --address-prefixes ``` и ```--address-prefix``` ниже в соответствии со сценарием, описанным в статье:

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >В приведенной выше статье для создания ресурсов используется расположение "Западная Европа", но в этой статье используется "Западно-центральная часть США". Внесите изменения, соответствующие расположению.

4. [Создайте учетную запись хранения](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) для виртуальной машины.

5. Создайте сетевой адаптер и IP-конфигурации, которые ему будут присвоены. Конфигурации можно добавлять, удалять или изменять по мере необходимости. В сценарии описаны следующие конфигурации.

    **IPConfig-1**

    Выполните указанные ниже команды, чтобы создать:

    - ресурс общедоступного IP-адреса с общедоступным статическим IP-адресом;
    - IP-конфигурацию с ресурсом общедоступного IP-адреса и динамическим частным IP-адресом.

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > За общедоступные IP-адреса взимается номинальная плата. Дополнительные сведения о ценах на IP-адреса см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses). Число общедоступных IP-адресов, которые можно использовать в одной подписке, ограничено. Сведения об ограничениях см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md#networking-limits).

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Выполните следующие команды, чтобы создать ресурс общедоступного IP-адреса и IP-конфигурацию с общедоступным статическим IP-адресом и частным статическим IP-адресом.
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Выполните следующие команды, чтобы создать IP-конфигурацию с частным динамическим IP-адресом без общедоступного IP-адреса.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >Хотя в этом руководстве все IP-конфигурации назначаются одному сетевому адаптеру, вы также можете назначить несколько IP-конфигураций любому сетевому адаптеру, связанному с виртуальной машиной. Изучите статью о создании виртуальной машины с несколькими сетевыми адаптерами.

6. [Создайте виртуальную машину Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Не забудьте удалить свойство ```  --availset-name myAvailabilitySet \ ```, которое не используется в этом сценарии. Используйте расположение, соответствующее вашему сценарию. 

    >[!WARNING] 
    > Шаг 6 из руководства по созданию виртуальной машины завершится неудачей, если нужный размер виртуальной машины не поддерживается в выбранном расположении. Выполните следующую команду, чтобы получить полный список виртуальных машин, например для расположения "Западно-центральная часть США": `azure vm sizes --location westcentralus`. Имя расположения можно изменить с учетом сценария.

    Например, чтобы использовать размер виртуальной машины Standard DS2 v2, просто добавьте свойство ```  --vm-size Standard_DS3_v2``` в команду ``` azure vm create ``` на шаге 6.

7. Введите следующую команду, чтобы просмотреть свойства сетевого адаптера и связанных с ним IP-конфигураций:

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```
8. Добавьте в операционную систему виртуальной машины частные IP-адреса, выполнив действия, соответствующие вашей операционной системе, как описано в разделе [Добавление IP-адресов в операционную систему виртуальной машины](#os-config) этой статьи.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Добавление IP-адресов в виртуальную машину

Выполнив следующие шаги, вы сможете назначить дополнительные частные или общедоступные IP-адреса существующим сетевым адаптерам. Примеры созданы на основе [сценария](#Scenario), описанного в этой статье.

1. Откройте интерфейс командной строки Azure и выполните в рамках одного сеанса командной строки все действия, описанные в этом разделе. Если интерфейс командной строки Azure еще не установлен, выполните инструкции из статьи [Установка Azure CLI](../xplat-cli-install.md), а затем войдите в учетную запись Azure.

2. Зарегистрируйтесь для использования общедоступной предварительной версии, выполнив шаг 2 в разделе **Создание виртуальной машины с несколькими IP-адресами**.

3. Выполните действия, описанные в одном из следующих разделов, с учетом задач.

    **Добавление частного IP-адреса**
    
    Чтобы назначить сетевому адаптеру частный IP-адрес, создайте IP-конфигурацию с помощью следующей команды.  Если вам нужен динамический частный IP-адрес, удалите из этой команды элемент ```-PrivateIpAddress 10.0.0.7```. Указываемый статический IP-адрес должен быть свободен в используемой подсети.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    Вы можете создать любое количество конфигураций, указывая для них уникальные имена и уникальные частные IP-адреса (если используются статические IP-адреса).

    **Добавление общедоступного IP-адреса**
    
    Чтобы добавить общедоступный IP-адрес, его нужно связать с новой или имеющейся IP-конфигурацией. Выполните шаги, описанные в одном из следующих разделов, в соответствии с задачами.

    > [!NOTE]
    > За общедоступные IP-адреса взимается номинальная плата. Дополнительные сведения о ценах на IP-адреса см. на странице [Цены на IP-адреса](https://azure.microsoft.com/pricing/details/ip-addresses). Число общедоступных IP-адресов, которые можно использовать в одной подписке, ограничено. Сведения об ограничениях см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md#networking-limits).
    >

    **Связывание ресурса с новой IP-конфигурацией**
    
    Чтобы добавить общедоступный IP-адрес в новую IP-конфигурацию, необходимо добавить и частный IP-адрес, так как все IP-конфигурации должны иметь частный IP-адрес. В конфигурацию можно добавить имеющийся ресурс общедоступного IP-адреса или создать новый. Чтобы создать ресурс, выполните следующую команду:
    
    ```azurecli
      azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

     Чтобы создать новую IP-конфигурацию с частным динамическим IP-адресом и связанным ресурсом общедоступного IP-адреса *myPublicIP3*, выполните следующую команду:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **Связывание ресурса с имеющейся IP-конфигурацией**
    Ресурс общедоступного IP-адреса может быть связан только с такой IP-конфигурацией, у которой еще нет такого ресурса. Чтобы определить, связан ли с конкретной IP-конфигурацией какой-либо общедоступный IP-адрес, выполните следующую команду:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    Найдите в возвращаемых данных строку, похожую на приведенную ниже.
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    Столбец **Общедоступный IP-адрес** для IP-конфигурации *IpConfig-3* пуст. Это означает, что сейчас с этой конфигурацией общедоступные IP-адреса не связаны. Вы можете добавить к конфигурации IpConfig-3 имеющийся ресурс общедоступного IP-адреса или создать новый ресурс, выполнив следующую команду:

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    Чтобы связать ресурс общедоступного IP-адреса с имеющейся IP-конфигурацией с именем *IPConfig-3*, выполните следующую команду:
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```

7. Просмотрите частные IP адреса и ресурсы общедоступных IP-адресов, назначенные сетевому адаптеру. Для этого введите следующую команду.

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    Вы должны увидеть результат, аналогичный приведенному ниже: 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. Добавьте в операционную систему виртуальной машины частные IP-адреса, которые вы ранее назначили сетевой карте. Для этого выполните инструкции из раздела [Добавление IP-адресов в операционную систему виртуальной машины](#os-config) этой статьи. Не добавляйте в операционную систему общедоступные IP-адреса.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

