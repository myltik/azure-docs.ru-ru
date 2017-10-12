---
title: "Краткое руководство: сеть кластера Azure Docker Swarm для Linux | Документация Майкрософт"
description: "Узнайте , как быстро создать кластер Docker Swarm для контейнеров Linux в Службе контейнеров Azure при помощи Azure CLI."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, Docker, Swarm
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2017
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 876135d62d548e155f4ebefd8bbd9d9cca8b87d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-docker-swarm-cluster"></a>Развертывание кластера Docker Swarm

В этом кратком руководстве объясняется, как развернуть кластер Docker Swarm с помощью Azure CLI. Затем в кластере будет развернуто и запущено многоконтейнерное приложение, состоящее из веб-интерфейса и экземпляра Redis. По завершении приложение будет доступно через Интернет.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Выходные данные:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Создание кластера Docker Swarm

Создайте кластер Docker Swarm в Службе контейнеров Azure с помощью команды [az acs create](/cli/azure/acs#create). 

В следующем примере создается кластер *mySwarmCluster* с одним главным узлом Linux и тремя узлами агентов Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

В некоторых случаях, например при использовании ограниченной пробной версии, доступ подписки Azure к ресурсам Azure ограничен. Если происходит сбой развертывания из-за ограничения доступных ядер, уменьшите количество агентов по умолчанию, добавив `--agent-count 1` в команду [az acs create](/cli/azure/acs#create). 

Через несколько минут выполнение команды завершается, и отображаются сведения о кластере в формате JSON.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

В этом кратком руководстве понадобится полное доменное имя (FQDN) образца Docker Swarm и пул агента Docker. Выполните следующую команду, чтобы вернуть оба IP-адреса.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

Выходные данные:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Создайте туннель SSH для главного узла Swarm. Замените `IPAddress` IP-адресом главного узла Swarm.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

Установите переменную среды `DOCKER_HOST`. Так вы сможете выполнять команды Docker с Docker Swarm без указания имени узла.

```bash
export DOCKER_HOST=:2375
```

Теперь вы готовы к запуску служб Docker с помощью Docker Swarm.


## <a name="run-the-application"></a>Выполнение приложения

Создайте файл с именем `docker-compose.yaml` и скопируйте в него следующее содержимое.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:redis-v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Выполните следующую команду, чтобы создать службу Azure для голосования.

```bash
docker-compose up -d
```

Выходные данные:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:redis-v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:redis-v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:redis-v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>Тестирование приложения

Перейдите по IP-адресу пула агентов Swarm, чтобы проверить приложение Azure для голосования.

![Изображение перехода к приложению Azure для голосования](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Удаление кластера
Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, выполните команду [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Получение кода

В этом кратком руководстве для создания службы Docker используются предварительно созданные образы контейнеров. Связанный с приложением код, Dockerfile и файл Compose доступны на сайте GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве мы развернули кластер Docker Swarm, а затем развернули в нем многоконтейнерное приложение.

Чтобы получить дополнительные сведения об интеграции Docker Swarm с Visual Studio Team Services, используйте CI или CD с помощью Docker Swarm и VSTS.

> [!div class="nextstepaction"]
> [Непрерывная интеграция и доставка с помощью Docker Swarm и VSTS](./container-service-docker-swarm-setup-ci-cd.md)