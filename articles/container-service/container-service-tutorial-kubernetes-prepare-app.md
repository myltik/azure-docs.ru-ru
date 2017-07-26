---
title: "Руководство по службе контейнеров Azure — подготовка приложения | Документация Майкрософт"
description: "Руководство по службе контейнеров Azure — подготовка приложения"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Создание образов контейнеров для использования со службой контейнеров Azure

В этом руководстве подготавливается приложение для Kubernetes. В частности, здесь рассматриваются такие шаги:  

> [!div class="checklist"]
> * Клонирование источника приложения из GitHub.  
> * Создание образов контейнеров из источника приложения.
> * Тестирование образов в локальной среде Docker.

В последующих руководствах эти образы контейнеров отправляются в реестр контейнеров Azure, а затем выполняются в кластерах Kubernetes, размещенных в Azure.

## <a name="before-you-begin"></a>Перед началом работы

Для выполнения действий, описанных в этом руководстве, необходимо базовое понимание основных понятий Docker, таких как контейнеры, образы контейнеров и основные команды Docker. При необходимости см. статью о [начале работы с Docker]( https://docs.docker.com/get-started/), чтобы ознакомиться с основами работы с контейнерами. 

Для работы с этим руководством требуется среда разработки Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Получение кода приложения

В этом руководстве используется простое приложение для голосования. Приложение состоит из веб-компонента интерфейсной части и серверной базы данных. 

Используйте git, чтобы скачать копию приложения в среду разработки.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

В каталоге приложения находятся предварительно созданные файлы Docker и файлы манифеста Kubernetes. Эти файлы используются для создания ресурсов в руководстве. 

## <a name="create-container-images"></a>Создание образов контейнеров

Чтобы создать образ контейнера для интерфейсной части приложения, используйте команду [docker build](https://docs.docker.com/engine/reference/commandline/build/).

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

Повторно выполните команду для образа серверной части контейнера.

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

После завершения используйте команду `docker images`, чтобы увидеть созданные образы. 

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

## <a name="test-application"></a>Тестирование приложения

Теперь, когда созданы два образа контейнеров, проверьте их в локальной среде разработки. 

Во-первых, создайте сеть Docker. Эта сеть используется для обмена данными между контейнерами.  

```bash
docker network create azure-vote
```

Запустите экземпляр образа контейнера серверной части с помощью команды `docker run`.

В этом примере файл базы данных mysql хранится внутри контейнера. После перемещения этого приложения в кластеры Kubernets для хранения файлов базы данных используется внешний том данных. Также переменные среды используются для настройки учетных данных MySQL.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Запустите экземпляр образа контейнера интерфейсной части.

Чтобы настроить подключение к базе данных используются переменные среды.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

После завершения выполните команду `docker ps`, чтобы просмотреть запущенные контейнеры.  

```bash
docker ps
```

Выходные данные:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

Перейдите к `http://localhost:8080`, чтобы увидеть работающее приложение. Инициализация приложения займет несколько секунд. Если возникнет ошибка, повторите попытку.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Теперь, когда функциональные возможности приложения проверены, запущенные контейнеры можно остановить и удалить. Не удаляйте образы контейнеров. Эти образы передаются в экземпляр реестра контейнеров Azure в следующем руководстве.

Остановите и удалите контейнер интерфейсной части с помощью команды [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-front
```

Остановите и удалите контейнер серверной части с помощью команды [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-back
```

Удалите сеть с помощью команды [docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/).

```bash
docker network rm azure-vote
```

По завершении у вас будет два образа контейнеров, из которых состоит приложение Azure для голосования.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы протестировали приложение и создали для него образы контейнеров. Были выполнены следующие действия:

> [!div class="checklist"]
> * Клонирование источника приложения из GitHub.  
> * Создание образов контейнеров из источника приложения.
> * Тестирование образов в локальной среде Docker.

Переходите к следующему руководству, чтобы узнать о хранении образов контейнеров в реестре контейнеров Azure.

> [!div class="nextstepaction"]
> [Развертывание реестра контейнеров Azure и его использование](./container-service-tutorial-kubernetes-prepare-acr.md)
