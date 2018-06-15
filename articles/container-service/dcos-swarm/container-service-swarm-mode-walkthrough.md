---
title: Краткое руководство. Кластер Azure Docker CE для Linux
description: Из этого краткого руководства вы узнаете, как создать кластер Docker CE для контейнеров Linux в службе контейнеров Azure при помощи Azure CLI.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: ''
ms.openlocfilehash: cd52982fc650d5fd3b4edd8513946a721306a451
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164097"
---
# <a name="deploy-docker-ce-cluster"></a>Развертывание кластера Docker CE

В этом кратком руководстве объясняется, как развернуть кластер Docker CE с помощью Azure CLI. Затем в кластере будет развернуто и запущено многоконтейнерное приложение, состоящее из веб-интерфейса и экземпляра Redis. По завершении приложение будет доступно через Интернет.

Выпуск Docker CE в службе контейнеров Azure находится на стадии предварительной версии и **его не следует использовать для рабочих нагрузок в рабочей среде**.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *ukwest*.

```azurecli-interactive
az group create --name myResourceGroup --location ukwest
```

Выходные данные:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "ukwest",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Создание кластера Docker Swarm

Создайте кластер Docker CE в службе контейнеров Azure с помощью команды [az acs create](/cli/azure/acs#az_acs_create). 

В следующем примере создается кластер *mySwarmCluster* с одним главным узлом Linux и тремя узлами агентов Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

В некоторых случаях, например при использовании ограниченной пробной версии, доступ подписки Azure к ресурсам Azure ограничен. Если происходит сбой развертывания из-за ограничения доступных ядер, уменьшите количество агентов по умолчанию, добавив `--agent-count 1` в команду [az acs create](/cli/azure/acs#az_acs_create). 

Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

В этом кратком руководстве необходимо полное доменное имя (FQDN) главного узла Docker Swarm и пула агента Docker. Выполните следующую команду для получения полных доменных имен примера и агента.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Выходные данные:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Создайте туннель SSH для главного узла Swarm. Замените `MasterFQDN` адресом полного доменного имени главного узла Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Установите переменную среды `DOCKER_HOST`. Так вы сможете выполнять команды Docker с Docker Swarm без указания имени узла.

```bash
export DOCKER_HOST=localhost:2374
```

Теперь вы готовы к запуску служб Docker с помощью Docker Swarm.


## <a name="run-the-application"></a>Выполнение приложения

Создайте файл с именем `azure-vote.yaml` и скопируйте в него следующее содержимое.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Выполните команду [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/), чтобы создать службу Azure для голосования.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Выходные данные:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Используйте команду [docker ps стека](https://docs.docker.com/engine/reference/commandline/stack_ps/) для развертывания приложения.

```bash
docker stack ps azure-vote
```

Как только `CURRENT STATE` каждой службы приобретет значение `Running`, приложение будет готово.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Тестирование приложения

Перейдите к полному доменному имени пула агентов Swarm, чтобы проверить приложение Azure для голосования.

![Изображение перехода к приложению Azure для голосования](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Удаление кластера
Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, выполните команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Получение кода

В этом кратком руководстве для создания службы Docker используются предварительно созданные образы контейнеров. Связанный с приложением код, Dockerfile и файл Compose доступны на сайте GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве мы развернули кластер Docker Swarm, а затем развернули в нем многоконтейнерное приложение.

Чтобы получить дополнительные сведения об интеграции Docker Swarm с Visual Studio Team Services, используйте CI или CD с помощью Docker Swarm и VSTS.

> [!div class="nextstepaction"]
> [Непрерывная интеграция и доставка с помощью Docker Swarm и VSTS](./container-service-docker-swarm-setup-ci-cd.md)
