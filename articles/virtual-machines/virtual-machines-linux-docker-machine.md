---
title: "Создание узлов Docker в Azure с помощью виртуальной машины Docker | Документация Майкрософт"
description: "Описывается использование машины Docker для создания узлов Docker в Azure."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
ms.assetid: 164b47de-6b17-4e29-8b7d-4996fa65bea4
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 8fe34e543d8860d80016d35d27159298c17e486e


---
# <a name="use-docker-machine-with-the-azure-driver"></a>Использование машины Docker с драйвером Azure
[Docker](https://www.docker.com/) — один из самых популярных подходов к виртуализации, использующий контейнеры Linux вместо виртуальных машин как способ изоляции данных приложения и вычислений при использовании общих ресурсов. В этой статье описывается, когда и как использовать [виртуальную машину Docker](https://docs.docker.com/machine/) (команду `docker-machine`) для создания виртуальных машин Linux в Azure, настроенных в качестве узла Docker для контейнеров Linux.

## <a name="create-vms-with-docker-machine"></a>Создание виртуальных машин с помощью машины Docker
Создайте виртуальные машины в Azure в качестве узла Docker с помощью команды `docker-machine create`, используя аргумент драйвера `azure` для параметра драйвера (`-d`), а также другие аргументы. 

В приведенном ниже примере используются значения по умолчанию, но в нем не открывается порт 80 на виртуальной машине для доступа к Интернету с целью проведения проверки с помощью контейнера nginx, `ops` устанавливается пользователем входа для SSH и вызывается `machine` новой виртуальной машины. 

Чтобы просмотреть параметры и их значения по умолчанию, введите `docker-machine create --driver azure`. Также можно ознакомиться с [документацией по драйверу Docker Azure](https://docs.docker.com/machine/drivers/azure/). (Имейте в виду, что если включена двухфакторная аутентификация, вам будет необходимо пройти аутентификацию с использованием второго фактора.)

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

Результаты должны выглядеть примерно так, как показано ниже, в зависимости от того, настроена ли в вашей учетной записи двухфакторная аутентификация.

```bash
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
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
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>Настройка оболочки Docker
Теперь введите `docker-machine env <VM name>` , чтобы узнать, что нужно сделать для настройки оболочки. 

```bash
docker-machine env machine
```

Будет выведена информация о среде наподобие приведенной ниже. Обратите внимание на то, что был назначен IP-адрес, который понадобится для тестирования виртуальной машины.

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

Вы можете либо выполнить предлагаемую команду настройки, либо задать переменные среды самостоятельно. 

## <a name="run-a-container"></a>Запуск контейнера
Теперь вы можете запустить простой веб-сервер, чтобы протестировать правильную работу всех компонентов. В этом примере мы используем стандартный образ nginx, указываем, что он должен прослушивать порт 80 и что при перезапуске виртуальной машины контейнер также должен перезапускаться (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Результаты должны быть примерно следующими:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Проверка контейнера
Проверьте запущенные контейнеры с помощью `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Чтобы узнать IP-адрес запущенного контейнера (если вы забыли адрес из команды `env`), введите `docker-machine ip <VM name>`:

![Запущенный контейнер nginx](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>Дальнейшие действия
При желании вы можете попробовать воспользоваться [расширением виртуальных машин Docker](virtual-machines-linux-dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) в Azure для выполнения этой же операции с помощью интерфейса командной строки Azure или шаблонов Azure Resource Manager. 

Дополнительные примеры работы с Docker см. в статье [Working with Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) (Работа с Docker), описывающей [демонстрационный проект](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect. Дополнительные инструкции по быстрому началу работы с помощью средств разработчика Azure из демонстрационного проекта HealthClinic.biz см. [здесь](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).




<!--HONumber=Nov16_HO3-->


