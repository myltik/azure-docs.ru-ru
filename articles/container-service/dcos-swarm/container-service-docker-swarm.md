---
title: Управление кластером Swarm в Azure с помощью API Docker
description: Развертывание контейнеров в кластер Docker Swarm в Службе контейнеров Azure
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 3f8d18bc053bc303ab124ba38c8621d4ee2e8cb8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
ms.locfileid: "26331551"
---
# <a name="container-management-with-docker-swarm"></a>Управление контейнерами с помощью Docker Swarm

Docker Swarm — это среда для развертывания контейнерной рабочей нагрузки в наборе узлов, объединенных в пул Docker. В Docker Swarm используется собственный API Docker. Поэтому рабочие процессы при управлении контейнерами Docker Swarm и отдельного узла контейнера почти не отличаются. Этот документ содержит простые примеры развертывания контейнерной рабочей нагрузки в экземпляре Docker Swarm, который развернут в службе контейнеров Azure. Дополнительные сведения о Docker Swarm см. в [документации по Docker Swarm на сайте Docker.com](https://docs.docker.com/swarm/).

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Предварительные требования для выполнения упражнений из этого документа:

[Создайте кластер Swarm в службе контейнеров Azure.](container-service-deployment.md)

[Подключитесь к кластеру Swarm в службе контейнеров Azure.](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>Развертывание нового контейнера
Чтобы создать новый контейнер в Docker Swarm, используйте команду `docker run` (при условии, что вы открыли туннель SSH для образцов согласно указанным выше предварительным условиям). В этом примере контейнер создается из образа `yeasy/simple-web` :

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

После создания контейнера выполните команду `docker ps` , чтобы получить сведения о контейнере. Обратите внимание, что здесь указан агент Swarm, в котором размещен контейнер:

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

К приложениям, которые работают в этом контейнере, теперь можно обращаться, используя общедоступное DNS-имя балансировщика нагрузки агента Swarm. Эти данные можно найти на портале Azure:  

![Результаты реального посещения](./media/container-service-docker-swarm/real-visit.jpg)  

По умолчанию для балансировщика нагрузки открыты порты 80, 8080 и 443. Если вы хотите подключиться к другому порту, необходимо открыть этот порт в Azure Load Balancer для пула агентов.

## <a name="deploy-multiple-containers"></a>Развертывание нескольких контейнеров
Если с помощью многократного выполнения команды docker run запущено несколько контейнеров, с помощью команды `docker ps` можно увидеть, на каком узле работает каждый из контейнеров. В примере ниже мы видим три контейнера, которые равномерно распределены между тремя агентами Swarm:  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Развертывание контейнеров с помощью Docker Compose
Docker Compose позволяет автоматизировать развертывание и настройку нескольких контейнеров. Для этого нужно создать туннель SSH и присвоить значение переменной DOCKER_HOST (см. предварительные требования выше).

Создайте на локальной системе файл docker-compose.yml. Для этого используйте следующий [пример](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Выполните команду `docker-compose up -d` , чтобы запустить развертывания контейнеров:

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

В результате будет возвращен список запущенных контейнеров. В нем будет указано, какие из них развернуты с помощью Docker Compose:

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Конечно, вы можете использовать команду `docker-compose ps` для просмотра только контейнеров, определенных в файле `compose.yml`.

## <a name="next-steps"></a>Дополнительная информация
[Дополнительные сведения о Docker Swarm](https://docs.docker.com/swarm/)

