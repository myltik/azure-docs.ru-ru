---
title: Запуск Ansible с помощью Bash в Azure Cloud Shell
description: Узнайте, как выполнять разные задачи Ansible с использованием Bash в Azure Cloud Shell.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, сборник тренировочных заданий, bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 9fe65f4cf10119002bcb7a3855d112d850e20f1a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150385"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Запуск Ansible с помощью Bash в Azure Cloud Shell

Из этого руководства вы узнаете, как выполнять разные задачи Ansible из Bash в Cloud Shell. Эти задачи включают подключение к виртуальной машине и создание модулей playbook Ansible для создания и удаления группы ресурсов Azure.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

- **Учетные данные Azure.** - [ См. сведения о создании учетных данных Azure и настройке Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials).

- **Настройте Azure Cloud Shell.** Если вы еще не работали с Azure Cloud Shell, в статье [Краткое руководство по Bash в Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) объясняется, как запустить и настроить Cloud Shell. Запустите выделенный веб-сайт для Cloud Shell:

[![Запуск Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Подключение к виртуальной машине с помощью Ansible
В ПО Ansible создан скрипт Python с названием [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py), который создает динамический список ресурсов Azure, отправляя запросы API к Azure Resource Manager. Ниже объясняется, как с помощью скрипта `azure_rm.py` подключиться к виртуальной машине Azure.

1. Откройте Bash в Cloud Shell. Тип оболочки указан в левой части окна Cloud Shell.

1. Если у вас нет виртуальной машины, введите следующие команды в Cloud Shell, чтобы создать виртуальную машину для тестирования:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Получите скрипт `azure_rm.py`, используя команду `wget` GNU:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Измените разрешения на доступ к скрипту `azure_rm.py`, используя команду `chmod`: В следующей команде используется параметр `+x`, чтобы разрешить выполнение указанного файла (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Подключитесь к своей виртуальной машине, используя [команду Ansible](https://docs.ansible.com/ansible/2.4/ansible.html): 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  Когда подключение будет установлено, отобразятся похожие результаты:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Если вы создали группу ресурсов и виртуальную машину в этом разделе:

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Запуск playbook в Cloud Shell
Команда [ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) выполняет модули playbook Ansible, запуская задачи на целевых узлах. В этом разделе объясняется, как использовать Cloud Shell для создания и выполнения двух модулей playbook: один — для создания группы ресурсов, а второй — для ее удаления. 

1. Создайте файл с именем `rg.yml`:

  ```azurecli-interactive
  vi rg.yml
  ```

1. Скопируйте следующее содержимое в окно Cloud Shell (теперь в нем находится экземпляр редактора VI):

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. Сохраните файл и закройте редактор VI. Для этого введите команду `:wq` и нажмите клавишу &lt;ВВОД>.

1. Используйте команду `ansible-playbook`, чтобы запустить модуль playbook`rg.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Должен отобразиться похожий результат:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Проверьте развертывание:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. После создания группы ресурсов создайте второй модуль playbook Ansible для удаления группы ресурсов:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Скопируйте следующее содержимое в окно Cloud Shell (теперь в нем находится экземпляр редактора VI):

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. Сохраните файл и закройте редактор VI. Для этого введите команду `:wq` и нажмите клавишу &lt;ВВОД>.

1. Используйте команду `ansible-playbook`, чтобы запустить модуль playbook`rg2.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Должен отобразиться похожий результат:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"] 
> [Создание базовой виртуальной машины в Azure с помощью Ansible](/azure/virtual-machines/linux/ansible-create-vm)
