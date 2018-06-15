---
title: Создание и передача образа виртуальной машины OpenBSD в Azure | Документация Майкрософт
description: Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл), содержащий операционную систему OpenBSD, для создания виртуальной машины Azure с помощью Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 6c0eae36874c6d2738385c4530cc208a0b1362c4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424310"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Создание и передача образа жесткого диска OpenBSD в Azure
В этой статье описывается, как создать и передать виртуальный жесткий диск, содержащий операционную систему OpenBSD. После передачи его можно использовать как свой собственный образ для создания виртуальной машины в Azure с помощью Azure CLI.


## <a name="prerequisites"></a>предварительным требованиям
В данной статье предполагается, что у вас есть следующие элементы:

* **Подписка Azure.** Если у вас нет учетной записи, то ее можно создать, что займет всего лишь несколько минут. Если у вас есть подписка MSDN, см. страницу [Ежемесячная сумма денег на счете в Azure для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). В противном случае узнайте, как [создать бесплатную пробную учетную запись](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI 2.0.** Обязательно установите последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).
* **Операционная система OpenBSD, установленная в VHD-файл.** На виртуальный жесткий диск необходимо установить поддерживаемую операционную систему OpenBSD ([версия 6.1 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/)). Существует несколько средств для создания VHD-файлов. Например, для создания VHD-файла и установки операционной системы можно использовать решение для виртуализации, например Hyper-V. Инструкции по установке и использованию Hyper-V см. в статье [Установка Hyper-V и создание виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Подготовка образа OpenBSD для Azure
На виртуальной машине, где вы установили ОС OpenBSD 6.1 с поддержкой Hyper-V, выполните следующие действия:

1. Если DHCP не был включен во время установки, включите службу следующим образом:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Настройте последовательную консоль.

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Настройте пакет установки следующим образом:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. По умолчанию пользователь `root` отключен в виртуальных машинах в Azure. Пользователи могут выполнять команды с повышенными привилегиями, используя команду `doas` на виртуальной машине OpenBSD. Doas включен по умолчанию. Дополнительные сведения см. на веб-сайте [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Установите и настройте необходимые компоненты для агента Azure следующим образом:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Последний выпуск агента Azure всегда можно найти на сайте [github](https://github.com/Azure/WALinuxAgent/releases). Установите агент следующим образом:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > После установки агента Azure рекомендуется проверить его работу.
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Отзовите систему, чтобы очистить ее и сделать пригодной для повторной подготовки. Приведенная ниже команда также удаляет последнюю подготовленную учетную запись пользователя и связанные с ней данные.

    ```sh
    waagent -deprovision+user -force
    ```

Теперь вы можете завершить работу виртуальной машины.


## <a name="prepare-the-vhd"></a>Подготовка VHD
Формат VHDX не поддерживается в Azure, поддерживается только **фиксированный VHD**. Преобразовать диск в формат фиксированного VHD можно с помощью диспетчера Hyper-V или командлета PowerShell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd). Например.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Создание и передача ресурсов хранилища
Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте учетную запись хранения с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create), чтобы передать VHD. Имя учетной записи хранения должно быть уникальным, поэтому введите собственное имя. В следующем примере создается учетная запись хранения с именем *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Для управления доступом к учетной записи хранения получите ключ хранилища с помощью команды [az storage account key list](/cli/azure/storage/account/keys#az_storage_account_keys_list) следующим образом:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Для логического разделения виртуальных жестких дисков создайте контейнер в учетной записи хранения с помощью команды [az storage container create](/cli/azure/storage/container#az_storage_container_create):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Наконец, отправьте ваш VHD с помощью команды [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) следующим образом:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Создание виртуальной машины из VHD
Можно создать виртуальную машину с помощью [примера скрипта](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) или напрямую с помощью команды [az vm create](/cli/azure/vm#az_vm_create). Чтобы указать переданный VHD OpenBSD, используйте параметр `--image` следующим образом:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Получите IP-адрес для виртуальной машины OpenBSD с помощью команды [az vm list-ip-addresses](/cli/azure/vm#list-ip-addresses) следующим образом:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Теперь вы можете подключаться к виртуальной машине OpenBSD по протоколу SSH в обычном режиме:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Дополнительная информация
Если вы хотите получить дополнительные сведения о поддержке Hyper-V в OpenBSD 6.1, посетите веб-сайты [OpenBSD 6.1](https://www.openbsd.org/61.html) и [hyperv.4](http://man.openbsd.org/hyperv.4).

Если требуется создать виртуальную машину из управляемого диска см. статью [Управляемые диски - az disk](/cli/azure/disk). 
