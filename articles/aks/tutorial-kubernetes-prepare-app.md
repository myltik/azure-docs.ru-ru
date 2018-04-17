---
title: Руководство по Kubernetes в Azure. Подготовка приложения
description: Руководство по AKS. Подготовка приложения
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8a2c2e53ed04cf00cc02135c5e5f82ded18fc2bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-prepare-application-for-azure-container-service-aks"></a>Руководство. Подготовка приложения для Службы контейнеров Azure (AKS)

В этом руководстве (часть один из восьми) выполняется подготовка многоконтейнерного приложения к использованию в Kubernetes. В частности, рассматриваются такие шаги:  

> [!div class="checklist"]
> * Клонирование источника приложения из GitHub.  
> * Создание образа контейнера из источника приложения.
> * Тестирование приложения в локальной среде Docker.

После выполнения всех действий в вашей локальной среде разработки станет доступным следующее приложение:

![Схема кластера Kubernetes в Аzure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

В последующих руководствах образ контейнера передается в реестр контейнеров Azure, а затем выполняется в кластере AKS.

## <a name="before-you-begin"></a>Перед началом работы

Для выполнения действий, описанных в этом руководстве, необходимо базовое понимание основных понятий Docker, таких как контейнеры, образы контейнеров и основные команды Docker. При необходимости см. руководство по [началу работы с Docker][docker-get-started], чтобы ознакомиться с базовыми сведениями о контейнерах. 

Для работы с этим руководством требуется среда разработки Docker. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе [Mac][docker-for-mac], [Windows][docker-for-windows] или [Linux][docker-for-linux].

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Таким образом мы рекомендуем полную среду разработки Docker.

## <a name="get-application-code"></a>Получение кода приложения

В этом руководстве используется простое приложение для голосования. Приложение состоит из веб-компонента интерфейсной части и серверного экземпляра Redis. Веб-компонент упаковывается в пользовательский образ контейнера, а для экземпляра Redis используется неизмененный образ из Docker Hub.  

Используйте git, чтобы скачать копию приложения в среду разработки.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Измените каталоги, чтобы использовать клонированный каталог.

```console
cd azure-voting-app-redis
```

Внутри каталога содержится исходный код приложения, предварительно созданный файл Docker Compose и файл манифеста Kubernetes. Эти файлы используются в руководстве. 

## <a name="create-container-images"></a>Создание образов контейнеров

С помощью [Docker Compose][docker-compose] можно автоматизировать создание дополнительных образов контейнеров и развертывание многоконтейнерных приложений.

Выполните файл `docker-compose.yaml`, чтобы создать образ контейнера, скачать образ Redis и запустить приложение.

```console
docker-compose up -d
```

После завершения выполните команду [docker images][docker-images], чтобы просмотреть созданные образы.

```console
docker images
```

Вы увидите, что были скачаны или созданы три образа. Образ `azure-vote-front` содержит приложение и использует образ `nginx-flask` в качестве основы. Образ `redis` используется для запуска экземпляра Redis.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Выполните команду [docker ps][docker-ps], чтобы просмотреть выполняемые контейнеры.

```console
docker ps
```

Выходные данные:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Локальное тестирование приложения

Перейдите по адресу http://localhost:8080, чтобы просмотреть запущенное приложение.

![Схема кластера Kubernetes в Аzure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Теперь, когда функциональные возможности приложения проверены, запущенные контейнеры можно остановить и удалить. Не удаляйте образы контейнеров. Образ `azure-vote-front` передается в экземпляр реестра контейнеров Azure в следующем руководстве.

Чтобы остановить запущенные контейнеры, выполните следующую команду:

```console
docker-compose stop
```

Чтобы удалить остановленные контейнеры и ресурсы, выполните следующую команду.

```console
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
> [Принудительная отправка образов в Реестр контейнеров Azure][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md