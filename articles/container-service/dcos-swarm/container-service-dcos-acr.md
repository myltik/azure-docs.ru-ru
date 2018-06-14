---
title: Использование ACR для кластера DC/OS Azure
description: Использование реестра контейнеров Azure с кластером DC/OS в Службе контейнеров Azure
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 90d449de19022b3b427e3d89d5beb18bbd36c6b4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28922898"
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Использование ACR в кластере DC/OS для развертывания приложения

В этой статье вы узнаете, как использовать реестр контейнеров Azure с кластером DC/OS. Использование ACR позволяет закрыто хранить образы контейнеров и управлять ими. В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Развертывание реестра контейнеров Azure (при необходимости).
> * Настройка проверки подлинности ACR в кластере DC/OS.
> * Отправка образа в реестр контейнеров Azure.
> * Запуск образа контейнера из реестра контейнеров Azure.

Для выполнения действий, описанных в этом руководстве, потребуется кластер ACS DC/OS. При необходимости его можно создать с помощью следующего [примера сценария](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

При необходимости создайте реестр контейнеров Azure с помощью команды [az acr create](/cli/azure/acr#az_acr_create). 

В следующем примере создается реестр со случайно созданным именем. При этом он настраивается с учетной записью администратора с помощью аргумента `--admin-enabled`.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

После создания реестра Azure CLI выводит данные, подобные следующим. Запишите `name` и `loginServer`, так как они понадобятся на следующих шагах.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Получите учетные данные реестра контейнеров с помощью команды [az acr credential show](/cli/azure/acr/credential). Замените `--name` значением, записанным на предыдущем шаге. Запишите один пароль, так как он потребуется в дальнейшем.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Дополнительные сведения о реестре контейнеров Azure см. в статье [Общие сведения о частных реестрах контейнеров Docker](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Управление проверкой подлинности ACR

Обычно, чтобы получить образ из закрытого реестра или передать образ в реестр, вам прежде всего нужно пройти проверку подлинности в реестре. Чтобы сделать это, используйте команду `docker login` на любом клиенте, которому требуется доступ к закрытому реестру. Так как кластер DC/OS может содержать множество узлов, которые должны пройти проверку подлинности с помощью ACR, полезно автоматизировать этот процесс для каждого узла. 

### <a name="create-shared-storage"></a>Создание общего хранилища

Этот процесс использует общую папку Azure, которая была подключена на каждом узле кластера. Если вы еще не настроили общее хранилище, ознакомьтесь со статьей [Создание и подключение файлового ресурса к кластеру DC/OS](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Настройка проверки подлинности ACR

Сначала получите полное доменное имя главного кластера DC/OS и сохраните его в переменной.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Создайте SSH-подключение к главному узлу (или первому главному узлу) кластера DC/OS. Измените имя пользователя, если при создании кластера использовалось значение не по умолчанию.

```azurecli-interactive
ssh azureuser@$FQDN
```

Выполните следующую команду, чтобы войти в реестр контейнеров Azure. Замените `--username` именем реестра контейнеров, а `--password` — одним из предоставленных паролей. Замените последний аргумент *mycontainerregistry.azurecr.io* в примере именем loginServer реестра контейнеров. 

Эта команда сохраняет значения для проверки подлинности локально в пути `~/.docker`.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Создайте сжатый файл, который содержит значения проверки подлинности реестра контейнера.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Скопируйте этот файл в общее хранилище кластера. Этот шаг делает файл доступным на всех узлах кластера DC/OS.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Отправка образа в ACR

Теперь из компьютера разработки или любой другой системы с установленным Docker создайте образ и отправьте его в реестр контейнеров Azure.

Создайте контейнер из образа Ubuntu.

```azurecli-interactive
docker run ubuntu --name base-image
```

Теперь добавьте контейнер в новый образ. Имя образа должно включать имя `loginServer` реестра контейнеров в формате `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Войдите в реестр контейнеров Azure. Замените имя именем loginServer, --username — именем реестра контейнеров, а --password — одним из предоставленных паролей.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Наконец, отправьте образ в реестр ACR. Этот пример отправляет образ с именем dcos-demo.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Запуск образа из ACR

Чтобы использовать образ из реестра ACR, создайте файл с именем *acrDemo.json* и скопируйте в него следующий текст. Замените имя образа именем loginServer реестра контейнеров и именем образа, например `loginServer/imageName`. Обратите внимание на свойство `uris`. Это свойство содержит расположение файла проверки подлинности реестра контейнеров, который в данном случае является общей папкой Azure, установленной на каждом узле в кластере DC/OS.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Разверните приложение с помощью CLI DC/OS.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы настроили кластер DC/OS для использования с реестром контейнеров Azure, в частности выполнили следующие задачи:

> [!div class="checklist"]
> * Развертывание реестра контейнеров Azure (при необходимости).
> * Настройка проверки подлинности ACR в кластере DC/OS.
> * Отправка образа в реестр контейнеров Azure.
> * Запуск образа контейнера из реестра контейнеров Azure.
