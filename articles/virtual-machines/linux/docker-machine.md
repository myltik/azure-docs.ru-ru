---
title: Создание узлов Linux в Azure с помощью Docker Machine | Документация Майкрософт
description: Описывается использование машины Docker для создания узлов Docker в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 26e54efc32aa316e1da93598cc861003aefff182
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125509"
---
# <a name="how-to-use-docker-machine-to-create-hosts-in-azure"></a>Создание узлов в Azure с помощью компьютера Docker | Документация Майкрософт
В этой статье представлены сведения о том, как создать узел в Azure с использованием [компьютера Docker](https://docs.docker.com/machine/). Команда `docker-machine` создает виртуальную машину Linux в Azure, после чего устанавливает Docker. Затем можно управлять узлами Docker в Azure с помощью аналогичных локальных средств и рабочих процессов. Для работы с docker-machine в Windows 10 необходимо использовать Linux bash.

## <a name="create-vms-with-docker-machine"></a>Создание виртуальных машин с помощью машины Docker
Сначала получите идентификатор подписки Azure с помощью команды [az account show](/cli/azure/account#az_account_show) следующим образом:

```azurecli
sub=$(az account show --query "id" -o tsv)
```

Создайте виртуальные машины узла Docker в Azure с помощью команды `docker-machine create`, используя драйвер *azure*. Кроме того, можно ознакомиться с [документацией по драйверу Docker Azure](https://docs.docker.com/machine/drivers/azure/).

В следующем примере создается виртуальная машина с именем *myVM* на основе плана Standard D2 v2, учетная запись пользователя с именем *azureuser* и открывается порт *80* на узле виртуальной машины. Выполните вход в учетную запись Azure и предоставьте компьютеру Docker разрешения на создание и управление ресурсами.

```bash
docker-machine create -d azure \
    --azure-subscription-id $sub \
    --azure-ssh-user azureuser \
    --azure-open-port 80 \
    --azure-size "Standard_D2_v2" \
    myvm
```

Результат должен быть аналогичным приведенному ниже:

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(myvm) Completed machine pre-create checks.
Creating machine...
(myvm) Querying existing resource group.  name="docker-machine"
(myvm) Creating resource group.  name="docker-machine" location="westus"
(myvm) Configuring availability set.  name="docker-machine"
(myvm) Configuring network security group.  name="myvm-firewall" location="westus"
(myvm) Querying if virtual network already exists.  rg="docker-machine" location="westus" name="docker-machine-vnet"
(myvm) Creating virtual network.  name="docker-machine-vnet" rg="docker-machine" location="westus"
(myvm) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(myvm) Creating public IP address.  name="myvm-ip" static=false
(myvm) Creating network interface.  name="myvm-nic"
(myvm) Creating storage account.  sku=Standard_LRS name="vhdski0hvfazyd8mn991cg50" location="westus"
(myvm) Creating virtual machine.  location="westus" size="Standard_A2" username="azureuser" osImage="canonical:UbuntuServer:16.04.0-LTS:latest" name="myvm
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env myvm
```

## <a name="configure-your-docker-shell"></a>Настройка оболочки Docker
Чтобы подключиться к узлу Docker в Azure, задайте соответствующие параметры подключения. Как указано в конце выходных данных, просмотрите сведения о соединении узла Docker следующим образом: 

```bash
docker-machine env myvm
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://40.68.254.142:2376"
export DOCKER_CERT_PATH="/Users/user/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env myvm)
```

Для задания параметров вы можете либо выполнить предлагаемую команду настройки (`eval $(docker-machine env myvm)`), либо задать переменные среды самостоятельно. 

## <a name="run-a-container"></a>Запуск контейнера
Чтобы увидеть контейнер в действии, запустите основной веб-сервер NGINX. Создайте контейнер с `docker run` и настройте порт 80 для веб-трафика следующим образом:

```bash
docker run -d -p 80:80 --restart=always nginx
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
ff3d52d8f55f: Pull complete
226f4ec56ba3: Pull complete
53d7dd52b97d: Pull complete
Digest: sha256:41ad9967ea448d7c2b203c699b429abe1ed5af331cd92533900c6d77490e0268
Status: Downloaded newer image for nginx:latest
675e6056cb81167fe38ab98bf397164b01b998346d24e567f9eb7a7e94fba14a
```

Проверьте запущенные контейнеры с помощью `docker ps`: В следующем примере в выходных данных отображается контейнер NGINX с настроенным портом 80:

```bash
CONTAINER ID    IMAGE    COMMAND                   CREATED          STATUS          PORTS                          NAMES
d5b78f27b335    nginx    "nginx -g 'daemon off"    5 minutes ago    Up 5 minutes    0.0.0.0:80->80/tcp, 443/tcp    festive_mirzakhani
```

## <a name="test-the-container"></a>Проверка контейнера
Получите общедоступный IP-адрес узла Docker следующим образом:


```bash
docker-machine ip myvm
```

Чтобы увидеть контейнер в действии, откройте веб-браузер и введите общедоступный IP-адрес, указанный в выходных данных предыдущей команды:

![Запущенный контейнер nginx](./media/docker-machine/nginx.png)

## <a name="next-steps"></a>Дополнительная информация
Кроме того, можно создавать узлы с помощью [расширения виртуальной машины Docker](dockerextension.md). Примеры использования Docker Compose см. в статье [Приступая к работе с Docker и Compose для определения и запуска многоконтейнерного приложения в Azure](docker-compose-quickstart.md).
