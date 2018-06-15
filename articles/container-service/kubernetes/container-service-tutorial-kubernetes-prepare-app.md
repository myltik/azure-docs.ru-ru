---
title: Руководство по службе контейнеров Azure — подготовка приложения
description: Руководство по службе контейнеров Azure — подготовка приложения
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2fe160652bf8df289d590722ef4024f0b3dd397c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164361"
---
# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Создание образов контейнеров для использования со службой контейнеров Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

В этом руководстве (часть 1 из 7) выполняется подготовка многоконтейнерного приложения к использованию в Kubernetes. В частности, рассматриваются такие шаги:  

> [!div class="checklist"]
> * Клонирование источника приложения из GitHub.  
> * Создание образа контейнера из источника приложения.
> * Тестирование приложения в локальной среде Docker.

После выполнения всех действий в вашей локальной среде разработки станет доступным следующее приложение:

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/azure-vote.png)

В последующих руководствах образ контейнера передается в реестр контейнеров Azure, а затем выполняется в кластере Kubernetes, размещенном в Azure.

## <a name="before-you-begin"></a>Перед началом работы

Для выполнения действий, описанных в этом руководстве, необходимо базовое понимание основных понятий Docker, таких как контейнеры, образы контейнеров и основные команды Docker. При необходимости см. статью о [начале работы с Docker]( https://docs.docker.com/get-started/), чтобы ознакомиться с основами работы с контейнерами. 

Для работы с этим руководством требуется среда разработки Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Таким образом мы рекомендуем полную среду разработки Docker.

## <a name="get-application-code"></a>Получение кода приложения

В этом руководстве используется простое приложение для голосования. Приложение состоит из веб-компонента интерфейсной части и серверного экземпляра Redis. Веб-компонент упаковывается в пользовательский образ контейнера, а для экземпляра Redis используется неизмененный образ из Docker Hub.  

Используйте git, чтобы скачать копию приложения в среду разработки.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Измените каталоги, чтобы использовать клонированный каталог.

```
cd azure-voting-app-redis
```

Внутри каталога содержится исходный код приложения, предварительно созданный файл Docker Compose и файл манифеста Kubernetes. Эти файлы используются в руководстве. 

## <a name="create-container-images"></a>Создание образов контейнеров

С помощью [Docker Compose](https://docs.docker.com/compose/) можно автоматизировать создание дополнительных образов контейнеров и развертывание многоконтейнерных приложений.

Выполните файл `docker-compose.yml`, чтобы создать образ контейнера, скачать образ Redis и запустить приложение.

```bash
docker-compose up -d
```

После завершения выполните команду [docker images](https://docs.docker.com/engine/reference/commandline/images/), чтобы увидеть созданные образы.

```bash
docker images
```

Вы увидите, что были скачаны или созданы три образа. Образ `azure-vote-front` содержит приложение и использует образ `nginx-flask` в качестве основы. Образ `redis` используется для запуска экземпляра Redis.

```bash
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Выполните команду [docker ps](https://docs.docker.com/engine/reference/commandline/ps/), чтобы просмотреть выполняемые контейнеры.

```bash
docker ps
```

Выходные данные:

```bash
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Локальное тестирование приложения

Перейдите к http://localhost:8080, чтобы увидеть работающее приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Теперь, когда функциональные возможности приложения проверены, запущенные контейнеры можно остановить и удалить. Не удаляйте образы контейнеров. Образ `azure-vote-front` передается в экземпляр реестра контейнеров Azure в следующем руководстве.

Чтобы остановить запущенные контейнеры, выполните следующую команду:

```bash
docker-compose stop
```

Чтобы удалить остановленные контейнеры и ресурсы, выполните следующую команду.

```bash
docker-compose down
```

По завершении у вас будет образ контейнера, содержащий приложение Azure Vote.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы протестировали приложение и создали для него образы контейнеров. Были выполнены следующие действия:

> [!div class="checklist"]
> * Клонирование источника приложения из GitHub.  
> * Создание образа контейнера из источника приложения.
> * Тестирование приложения в локальной среде Docker.

Переходите к следующему руководству, чтобы узнать о хранении образов контейнеров в реестре контейнеров Azure.

> [!div class="nextstepaction"]
> [Развертывание реестра контейнеров Azure и его использование](./container-service-tutorial-kubernetes-prepare-acr.md)
