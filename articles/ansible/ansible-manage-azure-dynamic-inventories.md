---
title: Управление динамическими списками Azure с помощью Ansible
description: Узнайте, как управлять динамическими списками Azure с помощью Ansible
ms.service: ansible
keywords: ansible, azure, разработка и операции, bash, cloudshell, динамические списки
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: f29f4ec64b79738cae2ad684610f4817739825a9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153115"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Управление динамическими списками Azure с помощью Ansible
Ansible можно использовать для извлечения информации из различных источников (включая облачные источники, такие как Azure) в *динамический список*. В этой статье вы настроите динамический список Ansible Azure с помощью [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md), создадите в нем две виртуальные машины, назначите одной из них тег и установите на ней Nginx.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

- **Учетные данные Azure.**  - [См. сведения о создании учетных данных Azure и настройке Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials).

## <a name="create-the-test-virtual-machines"></a>Создание тестовых виртуальных машин

1. Войдите на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Откройте [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Создайте группу ресурсов Azure для хранения виртуальных машин, используемых в этом руководстве.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Создайте две виртуальные машины Linux в Azure одним из следующих способов:

    - **Скрипт playbook Ansible**. В статье [Создание базовой виртуальной машины в Azure с помощью Ansible](/azure/virtual-machines/linux/ansible-create-vm) показано, как создать виртуальную машину с помощью скрипта playbook Ansible. При использовании скрипта playbook для определения одной или нескольких виртуальных машин убедитесь, что вместо пароля используется SSH-подключение.

    - **Azure CLI**. Выполните каждую из следующих команд в Cloud Shell, чтобы создать две виртуальные машины:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Добавление тега для виртуальной машины
Можно [использовать теги](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) для организации ресурсов Azure по определенным пользователем категориям. 

Выполните приведенную ниже команду [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag), чтобы добавить тег для виртуальной машины `ansible-inventory-test-vm1` с ключом `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Создание динамического списка
После определения виртуальных машин (и добавления тегов) необходимо создать динамический список. Ansible предоставляет скрипт Python с названием [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py), который создает динамический список ресурсов Azure, отправляя запросы API к Azure Resource Manager. Ниже описано, как использовать скрипт `azure_rm.py` для подключения двух тестовых виртуальных машин Azure:

1. Получите скрипт `azure_rm.py`, используя команду `wget` GNU:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Измените разрешения на доступ к скрипту `azure_rm.py`, используя команду `chmod`: В следующей команде используется параметр `+x`, чтобы разрешить выполнение указанного файла (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Подключитесь к своей группе ресурсов, используя [команду Ansible](https://docs.ansible.com/ansible/2.4/ansible.html): 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Когда подключение будет установлено, отобразятся результаты, аналогичные приведенным ниже выходным данным:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Включение тега виртуальной машины
Назначенный тег нужно включить. Для этого нужно экспортировать его в переменную среды `AZURE_TAGS` с помощью команды **export**:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Экспортировав тег, снова выполните команду `ansible`:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Теперь отображается только одна виртуальная машина (тег которой совпадает со значением, экспортированным в переменную среды **AZURE_TAGS**):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Настройка Nginx на виртуальной машине с тегом
Теги позволяют просто и быстро работать с подгруппами виртуальных машин. Предположим, вам необходимо установить Nginx только на виртуальных машинах, которым присвоен тег `nginx`. Ниже показано, как просто это можно сделать.

1. Создайте файл (в котором содержится скрипт playbook) с именем `nginx.yml` следующим образом:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Вставьте указанный ниже код в созданный файл `nginx.yml`:

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Запустите скрипт playbook `nginx.yml`:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. После запуска сборника отобразятся результаты, аналогичные приведенным ниже выходным данным:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Тестирование установки Nginx
В этом разделе показано, как проверить Nginx, установленный на виртуальной машине.

1. Извлеките IP-адрес виртуальной машины `ansible-inventory-test-vm1`, используя команду [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses). Возвращаемое значение (IP-адрес виртуальной машины) затем используется как параметр команды SSH, при помощи которого устанавливается подключение к виртуальной машине.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Команда [nginx -v](https://nginx.org/en/docs/switches.html) обычно используется для печати версии Nginx. Тем не менее ее также можно использовать, чтобы определить, установлен ли Nginx. Введите ее при установленном подключении к виртуальной машине `ansible-inventory-test-vm1`.

    ```azurecli-interactive
    nginx -v
    ```

1. После выполнения команды `nginx -v` отобразится версия Nginx (во второй строке). Это обозначает, что Nginx установлен.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Нажмите клавиши **&lt;CTRL>D**, чтобы отключить сеанс SSH.

1. При выполнении предыдущих шагов для виртуальной машины `ansible-inventory-test-vm2` отобразится информационное сообщение о том, где можно получить Nginx (это означает, что он еще не установлен на этом этапе):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Создание базовой виртуальной машины в Azure с помощью Ansible](/azure/virtual-machines/linux/ansible-create-vm)
