---
title: Использование расширения виртуальной машины Docker для Azure с Azure CLI 1.0 | Документация Майкрософт
description: Сведения об использовании расширения виртуальной машины Docker для быстрого и безопасного развертывания среды Docker в Azure с помощью шаблона Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 2f981a50887138660d26a9d011870a05f1270c94
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension-with-the-azure-cli-10"></a>Создание среды Docker в Azure с использованием расширения виртуальной машины Docker посредством Azure CLI 1.0
Docker — это популярная платформа управления контейнерами и работы с образами, которая позволяет быстро работать с контейнерами в Linux (а также в Windows). В Azure развертывание Docker можно выполнить несколькими разными способами в соответствии с конкретными потребностями. В этой статье рассматривается использование расширения виртуальной машины Docker и шаблонов Azure Resource Manager. 

Дополнительные сведения о различных методах развертывания, в том числе с помощью Docker Machine и служб контейнеров Azure, см. в следующих статьях:

* Чтобы быстро создать прототип приложения, можно создать один узел Docker с помощью [машины Docker](docker-machine.md).
* В более крупных стабильных средах можно использовать расширение виртуальной машины Docker для Azure, которое также поддерживает компонент [Docker Compose](https://docs.docker.com/compose/overview/), обеспечивающий согласованное развертывание контейнеров. В этой статье приведены подробные сведения об использовании расширения виртуальных машин Docker для Azure.
* Чтобы создать готовые к работе, масштабируемые среды с дополнительными средствами планирования и управления, можно развернуть [кластер Docker Swarm в службах контейнеров Azure](../../container-service/dcos-swarm/container-service-deployment.md).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](#azure-docker-vm-extension-overview) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](dockerextension.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager. 

## <a name="azure-docker-vm-extension-overview"></a>Общие сведения о расширении виртуальных машин Docker для Azure
Расширение виртуальных машин Docker для Azure устанавливает и настраивает управляющую программу Docker, клиент Docker и Docker Compose на виртуальной машине Linux. В отличие от использования только машины Docker или самостоятельного создания узла Docker с этим расширением вы получаете дополнительные элементы управления и компоненты. Благодаря этим дополнительным компонентам, таким как [Docker Compose](https://docs.docker.com/compose/overview/), расширение виртуальных машин Docker для Azure подходит для более надежных сред разработки или рабочих сред.

Шаблоны Azure Resource Manager определяют структуру всей среды. Они позволяют создавать и настраивать ресурсы, такие как виртуальные машины узла Docker, хранилище, элементы управления доступом на основе ролей (RBAC) и службу диагностики. Эти шаблоны можно повторно использовать для создания дополнительных развертываний ресурсов в согласованном состоянии. Дополнительные сведения об Azure Resource Manager и шаблонах см. в статье [Общие сведения об Azure Resource Manager ](../../azure-resource-manager/resource-group-overview.md). 

## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Развертывание шаблона с помощью расширения виртуальных машин Docker для Azure
Чтобы создать виртуальную машину Ubuntu, на которой установлено расширение виртуальной машины Docker для Azure (для установки и настройки узла Docker), мы используем готовый шаблон быстрого запуска. Шаблон можно найти в разделе [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)(Простое развертывание виртуальной машины Ubuntu с Docker). 

Кроме того, потребуется установить [последнюю версию интерфейса командной строки Azure](../../cli-install-nodejs.md) и выполнить вход в систему в режиме Resource Manager, как показано ниже.

```azurecli
azure config mode arm
```

Разверните шаблон с помощью интерфейса командной строки Azure, указав универсальный код ресурса (URI) шаблона. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *westus*. Укажите собственное имя группы ресурсов и расположение, как показано ниже.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

В ответ на запросы укажите имя учетной записи хранения, имя пользователя и пароль, а также DNS-имя. Вы должны увидеть результат, аналогичный приведенному ниже.

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Updating resource group myResourceGroup
info:    Updated resource group myResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mystorageaccount
adminUsername: azureuser
adminPassword: P@ssword!
dnsNameForPublicIP: mypublicidns
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Интерфейс командной строки Azure отобразит командную строку всего через несколько секунд, но создание и настройка узла Docker с помощью расширения виртуальных машин Docker для Azure еще не завершено. Подождите несколько минут до завершения развертывания. Сведения о состоянии узла Docker можно просмотреть с помощью команды `azure vm show`.

В следующем примере проверяется состояние виртуальной машины с именем *myDockerVM* (имя по умолчанию, указанное в шаблоне; не изменяйте его) в группе ресурсов *myResourceGroup*. Введите имя группы ресурсов, созданной на предыдущем шаге.

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

После выполнения команды `azure vm show` вы должны увидеть результат, аналогичный приведенному ниже.

```azurecli
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/myresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
[...]
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mypublicdns.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

В начале выходных данных отображается состояние **ProvisioningState** виртуальной машины. Если отображается состояние *Succeeded*, значит, развертывание завершено, и вы можете подключиться к виртуальной машине по протоколу SSH.

В конце выходных данных *FQDN* отображает полное доменное имя узла Docker. Это полное доменное имя будет использовано для подключения к узлу Docker по протоколу SSH на последующих шагах.

## <a name="deploy-your-first-nginx-container"></a>Развертывание первого контейнера nginx
После завершения развертывания установите SSH-подключение к новому узлу Docker из локального компьютера. Введите собственное имя пользователя и полное доменное имя, как показано ниже.

```bash
ssh ops@mypublicdns.westus.cloudapp.azure.com
```

После входа в систему узла Docker запустите контейнер nginx.

```bash
sudo docker run -d -p 80:80 nginx
```

После скачивания образа nginx и запуска контейнера результаты должны выглядеть примерно так, как показано в следующем примере.

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Проверьте состояние контейнеров, запущенных на узле Docker, следующим образом.

```bash
sudo docker ps
```

Результаты должны выглядеть примерно так, как показано ниже. В них должно быть указано, что контейнер nginx запущен, а TCP-порты 80 и 443 перенаправлены.

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Чтобы увидеть контейнер в действии, откройте веб-браузер и введите полное доменное имя узла Docker.

![Запущенный контейнер nginx](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Пример шаблона расширения виртуальной машины Docker для Azure
В предыдущем примере использовался готовый шаблон быстрого запуска. Расширение виртуальной машины Docker для Azure можно также развернуть с помощью собственных шаблонов Resource Manager. Для этого добавьте следующий код в свои шаблоны Resource Manager, указав вместо *vmName* имя своей виртуальной машины.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Подробное пошаговое руководство по использованию шаблонов Resource Manager см. в статье [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Дополнительная информация
Возможно, вам потребуется [настроить TCP-порт управляющей программы Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), [ознакомиться с параметрами безопасности Docker](https://docs.docker.com/engine/security/security/) или развернуть контейнеры с помощью [Docker Compose](https://docs.docker.com/compose/overview/). Дополнительные сведения о расширении виртуальных машин Docker для Azure см. в [репозитории GitHub](https://github.com/Azure/azure-docker-extension/).

Дополнительные сведения о вариантах развертывания Docker в Azure см. в следующих источниках:

* [Использование машины Docker с драйвером Azure](docker-machine.md)  
* [Приступая к работе с решениями Docker и Compose для определения и запуска многоконтейнерного приложения на виртуальной машине Azure](docker-compose-quickstart.md)
* [Развертывание кластера службы контейнеров Azure](../../container-service/dcos-swarm/container-service-deployment.md)

