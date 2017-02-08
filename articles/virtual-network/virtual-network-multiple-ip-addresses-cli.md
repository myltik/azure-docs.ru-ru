---
title: "Назначение виртуальным машинам нескольких IP-адресов с помощью интерфейса командной строки Azure | Документация Майкрософт"
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
ms.sourcegitcommit: 482e0d8084d84f9a3170180c2e5414ca77364da8
ms.openlocfilehash: 6fb458d47173b4922f085e8b1e6339cabefc7da6


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Назначение виртуальным машинам нескольких IP-адресов с помощью интерфейса командной строки Azure

> [!div class="op_single_selector"]
> * [Портал](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-network-multiple-ip-addresses-cli.md)

К виртуальной машине Azure можно подключить один или несколько сетевых адаптеров. Каждому такому адаптеру статически или динамически назначается один или несколько общедоступных или частных IP-адресов. Назначение нескольких IP-адресов виртуальной машине дает следующие возможности:

* Возможность размещать на одном сервере несколько веб-сайтов или служб с разными IP-адресами и SSL-сертификатами.
* возможность использовать виртуальную машину в качестве виртуального сетевого устройства, такого как брандмауэр или балансировщик нагрузки.
* Возможность добавления любых частных IP-адресов любых сетевых карт во внутренний пул Azure Load Balancer. Раньше во внутренний пул можно было добавить только основной IP-адрес для основной сетевой карты. Дополнительные сведения о балансировке нагрузки в конфигурациях с несколькими IP-адресами см. в [этой статье](../load-balancer/load-balancer-multiple-ip.md).

Каждому сетевому адаптеру, подключенному к виртуальной машине, присвоена одна или несколько конфигураций IP-адресов. Каждая конфигурация получает один статический или динамический частный IP-адрес. Также каждой конфигурации может быть присвоен один ресурс общедоступного IP-адреса. Ресурс общедоступного IP-адреса включает один динамический или статический IP-адрес. Дополнительные сведения об IP-адресах в Azure см. в статье [IP-адреса в Azure](virtual-network-ip-addresses-overview-arm.md).

В этой статье объясняется, как с помощью PowerShell назначить несколько IP-адресов виртуальной машине, созданной по модели развертывания с помощью Azure Resource Manager. Для ресурсов, созданных с помощью классической модели развертывания, нельзя назначить несколько IP-адресов. Дополнительные сведения о моделях развертывания Azure см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../resource-manager-deployment-model.md) (Развертывание с помощью Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Сценарий
Создается виртуальная машина с одним сетевым адаптером. Она подключается к виртуальной сети. Виртуальной машине нужно присвоить три разных *частных* и два *общедоступных* IP-адреса. IP-адреса назначаются следующим IP-конфигурациям.

* **IPConfig-1:** назначает *динамический* частный IP-адрес (по умолчанию) и *статический* общедоступный IP-адрес.
* **IPConfig-2:** назначает *статический* частный IP-адрес и *статический* общедоступный IP-адрес.
* **IPConfig-3:** назначает *динамический* частный IP-адрес и не назначает общедоступный IP-адрес.
  
    ![Несколько IP-адресов](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Когда создается сетевой адаптер, с ним связываются IP-конфигурации. Когда создается виртуальная машина, к ней подключается сетевой адаптер. Типы IP-адресов в этом сценарии указаны только для примера. Вы можете использовать любые типы IP-адресов и назначений, какие вам потребуются.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Создание виртуальной машины с несколькими IP-адресами

Вы можете создать пример виртуальной машины с несколькими IP-адресами, как описано в нашем сценарии. Измените имена переменных и типы IP-адресов в соответствии с требованиями этой реализации.

1. Установите и настройте интерфейс командной строки Azure, следуя инструкциям в [этой статье](../xplat-cli-install.md), а затем войдите в учетную запись Azure.

2. Зарегистрируйтесь для получения предварительной версии, отправив с помощью [этой ссылки](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) сообщение электронной почты. Укажите в нем идентификатор своей подписки и опишите предполагаемое использование. Не переходите к следующим этапам, пока не будут выполнены следующие условия:
    - вы получите по электронной почте уведомление о возможности использовать предварительную версию;
    - вы выполните все инструкции, приведенные в этом сообщении электронной почты.
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

2. Зарегистрируйтесь для получения предварительной версии, отправив с помощью [этой ссылки](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) сообщение электронной почты. Укажите в нем идентификатор своей подписки и опишите предполагаемое использование. Не переходите к следующим этапам, пока не будут выполнены следующие условия:
    - вы получите по электронной почте уведомление о возможности использовать предварительную версию;
    - вы выполните все инструкции, приведенные в этом сообщении электронной почты.

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


<!--HONumber=Dec16_HO1-->


