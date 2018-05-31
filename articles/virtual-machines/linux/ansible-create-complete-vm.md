---
title: Использование Ansible для создания готовой виртуальной машины Linux в Azure | Документы Майкрософт
description: Узнайте, как использовать Ansible для создания готовой среды виртуальных машин Linux в Azure и управления ею.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 22b580e74ec412763b9c34a7fa2fea97c8a277d0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896186"
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Создание готовой среды виртуальных машин Linux в Azure с помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления виртуальными машинами в Azure так же, как любым другим ресурсом. В этой статье показано, как создать готовую среду Linux и вспомогательные ресурсы с помощью Ansible. Вы также можете узнать, как [создать простейшую виртуальную машину с помощью Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>предварительным требованиям
Для управления ресурсами Azure с помощью Ansible необходимо следующее:

- Ansible и модули SDK Python для Azure, установленные в системе узла.
    - Установленный экземпляр Ansible в [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts) или [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2).
- Учетные данные Azure, настроенные для использования в Ansible.
    - [Создание учетных данных Azure и настройка Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. 
    - Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). Вы также можете использовать [Cloud Shell](/azure/cloud-shell/quickstart) из своего браузера.


## <a name="create-virtual-network"></a>Создание виртуальной сети
Следующий раздел в скрипте playbook Ansible создает виртуальную сеть с именем *myVnet* в адресном пространстве *10.0.0.0/16*:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Для добавления подсети следующий раздел создает подсеть с именем *mySubnet* в виртуальной сети *myVnet*:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Создание общедоступного IP-адреса
Для доступа к ресурсам через Интернет создайте общедоступный IP-адрес и назначьте его виртуальной машине. В следующем разделе скрипта playbook Ansible создается общедоступный IP-адрес с именем *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Создание группы безопасности сети
Группы безопасности сети контролируют передачу исходящего и входящего сетевого трафика виртуальной машины. В следующем разделе скрипта playbook Ansible создается группа безопасности сети с именем *myNetworkSecurityGroup* и определяется правило, разрешающее передачу трафика SSH через TCP-порт 22:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Создание виртуальных сетевых адаптеров
Виртуальный сетевой адаптер позволяет виртуальной машине подключаться к определенной виртуальной сети, общедоступному IP-адресу и группе безопасности сети. В следующем разделе скрипта Ansible создается виртуальный сетевой адаптер с именем *myNIC*, подключенный к созданным ресурсам виртуальной сети:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Создание виртуальной машины
Последним шагом является создание виртуальной машины и использование всех созданных ресурсов. В следующем разделе скрипта playbook Ansible создается виртуальная машина с именем *myVM* и подключается виртуальный сетевой адаптер с именем *myNIC*. Введите собственные данные полного открытого ключа в паре *key_data* следующим образом:

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys: 
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Завершение скрипта playbook Ansible
Чтобы объединить все эти разделы, создайте скрипт playbook Ansible с именем *azure_create_complete_vm.yml* и вставьте следующее содержимое. Введите собственные данные полного открытого ключа в паре *key_data*:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible требуется группа ресурсов, в которой будут развертываться все ресурсы. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/vm#az_vm_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Чтобы создать готовую среду виртуальных машин с помощью Ansible, запустите скрипт playbook следующим образом:

```bash
ansible-playbook azure_create_complete_vm.yml
```

Ниже приведен пример выходных данных, в котором показано, что виртуальная машина успешно создана:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Дополнительная информация
В этом примере создается готовая среда виртуальных машин, включающая необходимые ресурсы виртуальной сети. Пример создания виртуальной машины непосредственно на основе существующих сетевых ресурсов с параметрами по умолчанию см. в статье [Создание ВМ](ansible-create-vm.md).
