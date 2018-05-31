---
title: Использование Ansible для создания базовой виртуальной машины Linux в Azure | Документы Майкрософт
description: Узнайте, как использовать Ansible для создания базовой виртуальной машины Linux в Azure и управления ею.
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
ms.openlocfilehash: a2bf047d5a08bfd3df6a6c76116d2b9b9ab81fad
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896169"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Создание базовой виртуальной машины в Azure с помощью Ansible
Ansible позволяет автоматизировать развертывание и настройку ресурсов в среде. Ansible можно использовать для управления виртуальными машинами в Azure так же, как любым другим ресурсом. В этой статье показано, как создать базовую виртуальную машину с помощью Ansible. Вы также можете узнать, как [создать готовую среду виртуальных машин с помощью Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>предварительным требованиям
Для управления ресурсами Azure с помощью Ansible необходимо следующее:

- Ansible и модули SDK Python для Azure, установленные в системе узла.
    - Установленный экземпляр Ansible в [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts) или [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2).
- Учетные данные Azure, настроенные для использования в Ansible.
    - [Создание учетных данных Azure и настройка Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. 
    - Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). Вы также можете использовать [Cloud Shell](/azure/cloud-shell/quickstart) из своего браузера.


## <a name="create-supporting-azure-resources"></a>Создание вспомогательных ресурсов Azure
В этом примере вы создадите модуль runbook, который развертывает виртуальную машину в существующей инфраструктуре. Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/vm#az_vm_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную сеть для виртуальной машины с помощью команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). В следующем примере создаются виртуальная сеть *myVnet* и подсеть *mySubnet*.

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Создание и запуск скрипта playbook Ansible
Создайте скрипт playbook Ansible с именем *azure_create_vm.yml* и вставьте приведенное ниже содержимое. В этом примере создается одна виртуальная машина и настраиваются учетные данные SSH. Введите собственные данные полного открытого ключа в паре *key_data* следующим образом:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
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
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Чтобы создать виртуальную машину с помощью Ansible, запустите скрипт playbook следующим образом:

```bash
ansible-playbook azure_create_vm.yml
```

Ниже приведен пример выходных данных, в котором показано, что виртуальная машина успешно создана:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>Дополнительная информация
В этом примере создается виртуальная машина в существующей группе ресурсов с уже развернутой виртуальной сетью. Более подробный пример использования Ansible для создания вспомогательных ресурсов, таких как виртуальная сеть и правила группы безопасности сети, см. в статье [Создание готовой среды виртуальных машин с помощью Ansible](ansible-create-complete-vm.md).
