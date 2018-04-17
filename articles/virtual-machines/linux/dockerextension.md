---
title: Использование расширения виртуальной машины Docker для Azure | Документация Майкрософт
description: Узнайте, как использовать расширение виртуальной машины Docker для быстрого и безопасного развертывания среды Docker в Azure с помощью шаблонов Resource Manager и Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 1e5a4fcfd758c12213d6de7d0f5cfcc78531ee97
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Создание среды Docker в Azure с помощью расширения виртуальной машины Docker
Docker — это популярная платформа для управления контейнерами и работы с образами, которая позволяет быстро работать с контейнерами в Linux. В Azure развертывание Docker можно выполнить несколькими разными способами в соответствии с конкретными потребностями. В этой статье рассматривается использование расширения виртуальной машины Docker и шаблонов Azure Resource Manager с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](dockerextension-nodejs.md).

> [!WARNING]
> Расширение виртуальной машины Azure Docker для Linux не рекомендуется использовать, его поддержка будет прекращена с ноября 2018 г.
> Расширение просто устанавливает Docker, поэтому лучше использовать для этой цели альтернативные варианты, например cloud-init или расширение настраиваемых скриптов. Дополнительные сведения об использовании cloud-init см. в статье [Как настроить виртуальную машину Linux при первой загрузке](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Общие сведения о расширении виртуальных машин Docker для Azure
Расширение виртуальных машин Docker для Azure устанавливает и настраивает управляющую программу Docker, клиент Docker и Docker Compose на виртуальной машине Linux. В отличие от использования только машины Docker или самостоятельного создания узла Docker с этим расширением вы получаете дополнительные элементы управления и компоненты. Благодаря этим дополнительным компонентам, таким как [Docker Compose](https://docs.docker.com/compose/overview/), расширение виртуальных машин Docker для Azure подходит для более надежных сред разработки или рабочих сред.

Дополнительные сведения о различных методах развертывания, в том числе с помощью Docker Machine и служб контейнеров Azure, см. в следующих статьях:

* Чтобы быстро создать прототип приложения, можно создать один узел Docker с помощью [машины Docker](docker-machine.md).
* Чтобы создать готовые к работе масштабируемые среды с дополнительными средствами планирования и управления, можно развернуть кластер [Kubernetes](../../container-service/kubernetes/index.yml) или [Docker Swarm](../../container-service/dcos-swarm/index.yml) в Службах контейнеров Azure.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Развертывание шаблона с помощью расширения виртуальных машин Docker для Azure
Чтобы создать виртуальную машину Ubuntu, на которой установлено расширение виртуальной машины Docker для Azure (для установки и настройки узла Docker), мы используем готовый шаблон быстрого запуска. Шаблон можно найти в разделе [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)(Простое развертывание виртуальной машины Ubuntu с Docker). Вам нужно установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Затем, выполнив команду [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create), разверните виртуальную машину с расширением виртуальной машины Docker для Azure с помощью [этого шаблона Azure Resource Manager из репозитория GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). При запросе укажите собственные уникальные значения для параметров *newStorageAccountName*, *adminUsername*, *adminPassword* и *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Подождите несколько минут до завершения развертывания.


## <a name="deploy-your-first-nginx-container"></a>Развертывание первого контейнера NGINX
Для просмотра сведений о виртуальной машине, включая DNS-имя, можно использовать команду [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

Подключитесь к новому узлу Docker по протоколу SSH. Укажите имя пользователя и DNS-имя, созданное на предыдущих шагах:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

После входа в систему узла Docker запустите контейнер NGINX.

```bash
sudo docker run -d -p 80:80 nginx
```

После скачивания образа NGINX и запуска контейнера результаты должны выглядеть примерно так, как показано в следующем примере.

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

Результаты должны выглядеть примерно так, как показано ниже. В них должно быть указано, что контейнер NGINX запущен, а TCP-порты 80 и 443 перенаправлены.

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Чтобы увидеть контейнер в действии, откройте веб-браузер и введите DNS-имя узла Docker.

![Запущенный контейнер nginx](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Пример шаблона расширения виртуальной машины Docker для Azure
В предыдущем примере использовался готовый шаблон быстрого запуска. Расширение виртуальной машины Docker для Azure можно также развернуть с помощью собственных шаблонов Resource Manager. Для этого добавьте следующий код в свои шаблоны Resource Manager, задав вместо `vmName` имя своей виртуальной машины.

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
    "typeHandlerVersion": "1.*",
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

