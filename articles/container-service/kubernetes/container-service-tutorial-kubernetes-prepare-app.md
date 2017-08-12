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
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 851ce819b9a1a0d917981223cc54e959b3306709
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Создание образов контейнеров для использования со службой контейнеров Azure

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

## <a name="get-application-code"></a>Получение кода приложения

В этом руководстве используется простое приложение для голосования. Приложение состоит из веб-компонента интерфейсной части и серверного экземпляра Redis. Веб-компонент упаковывается в пользовательский образ контейнера, а для экземпляра Redis используется неизмененный образ из Docker Hub.  

Используйте git, чтобы скачать копию приложения в среду разработки.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Внутри клонированного каталога содержится исходный код приложения, предварительно созданный файл Docker Compose, а также файл манифеста Kubernetes. Эти файлы используются для создания ресурсов в руководстве. 

## <a name="create-container-images"></a>Создание образов контейнеров

С помощью [Docker Compose](https://docs.docker.com/compose/) можно автоматизировать создание дополнительных образов контейнеров и развертывание многоконтейнерных приложений.

Выполните файл docker-compose.yaml, чтобы создать образ контейнера, скачать образ Redis и запустить приложение.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yaml up -d
```

После завершения выполните команду [docker images](https://docs.docker.com/engine/reference/commandline/images/), чтобы увидеть созданные образы.

```bash
docker images
```

Вы увидите, что были скачаны или созданы три образа. Образ *azure-vote-front* содержит приложение. Он был создан на основе образа *nginx-flask*. Образ Redis был скачан из Docker Hub.

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

Перейдите по адресу http://localhost:8080, чтобы просмотреть запущенное приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Теперь, когда функциональные возможности приложения проверены, запущенные контейнеры можно остановить и удалить. Не удаляйте образы контейнеров. Образ *azure-vote-front* будет отправлен в экземпляр реестра контейнеров Azure в следующем руководстве.

Чтобы остановить запущенные контейнеры, выполните следующую команду:

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yaml stop
```

Чтобы удалить остановленные контейнеры, выполните следующую команду:

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yaml rm
```

По завершении у вас будет образ контейнера, содержащий приложение Azure Vote.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы протестировали приложение и создали для него образы контейнеров. Были выполнены следующие действия:

> [!div class="checklist"]
> * Клонирование источника приложения из GitHub.  
> * Создание образа контейнера из источника приложения.
> * Тестирование приложения в локальной среде Docker.

Переходите к следующему руководству, чтобы узнать о хранении образов контейнеров в реестре контейнеров Azure.

> [!div class="nextstepaction"]
> [Развертывание реестра контейнеров Azure и его использование](./container-service-tutorial-kubernetes-prepare-acr.md)
