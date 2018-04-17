---
title: Отправка образа Docker в частный реестр Azure
description: Отправка образов Docker в частный реестр контейнеров в Azure и их получение с помощью интерфейса командной строки Docker
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8fc04ec77a101e08bfde22df76e845b87f8c316e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Отправка первого образа в частный реестр контейнеров Docker с помощью интерфейса командной строки Docker

В реестре контейнеров Azure хранятся частные образы контейнеров [Docker](http://hub.docker.com), а также осуществляется управление ими (подобно тому, как в [Docker Hub](https://hub.docker.com/) хранятся общедоступные образы Docker). [Интерфейс командной строки Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) позволяет [входить](https://docs.docker.com/engine/reference/commandline/login/) в реестр контейнеров, а также выполнять [отправку](https://docs.docker.com/engine/reference/commandline/push/), [извлечение](https://docs.docker.com/engine/reference/commandline/pull/) и другие операции.

Выполняя следующие действия, вы скачаете официальный [образ Nginx](https://store.docker.com/images/nginx) из общедоступного реестра Docker Hub, поместите его в частный реестр контейнеров Azure, отправите его в свой реестр, а затем извлечете его от туда.

## <a name="prerequisites"></a>предварительным требованиям

* **Реестр контейнеров Azure.** Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI 2.0](container-registry-get-started-azure-cli.md).
* **Интерфейс командной строки Docker**. Установите [Docker](https://docs.docker.com/engine/installation/), чтобы настроить локальный компьютер в качестве узла Docker и получить доступ к командам Docker CLI.

## <a name="log-in-to-a-registry"></a>Вход в раздел реестра

Существуют [несколько способов выполнить аутентификацию](container-registry-authentication.md) в частном реестре контейнеров. При работе в командной строке мы рекомендуем выполнить команду Azure CLI [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Например, чтобы войти в реестр с именем *myregistry*, выполните следующую команду:

```azurecli
az acr login --name myregistry
```

Вы также можете выполнить вход с помощью команды [docker login](https://docs.docker.com/engine/reference/commandline/login/). Следующая команда передает идентификатор и пароль [субъекта-службы](../active-directory/active-directory-application-objects.md) Azure Active Directory. Например, [назначение субъекта-службы](container-registry-authentication.md#service-principal) для реестра позволяет автоматизировать некоторые сценарии.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

По завершению обе команды возвращают `Login Succeeded`. Если вы используете `docker login`, также может появиться предупреждение системы безопасности, рекомендующее использовать параметр `--password-stdin`. Хотя его использование выходит за рамки данной статьи, мы рекомендуем следовать данной рекомендации. Чтобы получить дополнительные сведения, ознакомьтесь с описанием команды [docker login](https://docs.docker.com/engine/reference/commandline/login/).

> [!TIP]
> Всегда указывайте полное имя реестра (все знаки в нижнем реестре), когда используете `docker login` и когда отмечаете изображения тегами для отправки в свой реестр. В примерах этой статьи полное доменное имя выглядит так: *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Извлечение официального образа Nginx

Сначала общедоступный образ Nginx нужно отправить на локальный компьютер.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Локальный запуск контейнера

Выполните указанную ниже команду [docker run](https://docs.docker.com/engine/reference/run/), чтобы запустить локальный экземпляр контейнера Nginx в интерактивном режиме (`-it`) на порте 8080. Аргумент `--rm` указывает, что контейнер следует удалить, когда вы его остановите.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Перейдите по адресу [http://localhost:8080](http://localhost:8080), чтобы просмотреть веб-страницу по умолчанию, обслуживаемую Nginx в выполняющемся контейнере. Вы должны увидеть страницу, аналогичную показанной ниже:

![Nginx на локальном компьютере](./media/container-registry-get-started-docker-cli/nginx.png)

Так как вы запустили контейнер в интерактивном режиме с помощью `-it`, вы можете увидеть выходные данные сервера Nginx в командной строке после перемещения к нему в браузере.

Чтобы остановить и удалить контейнер, нажмите `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Создание псевдонима образа

Выполните команду [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), чтобы создать псевдоним образа с полным путем к вашему реестру. Чтобы избежать беспорядка в корне реестра, эта команда указывает пространство имен `samples`.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Дополнительные сведения о расстановке тегов с помощью пространства имен см. в разделе [Пространства имен репозитория](container-registry-best-practices.md#repository-namespaces) статьи [Рекомендации по использованию реестра контейнеров Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Отправка образа в реестр

Теперь, когда вы отметили образ с абсолютным путем к частному реестру тегами, его можно отправить в реестр, выполнив команду [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Извлечение образа из реестра

Выполните команду [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), чтобы извлечь образ из реестра:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Запуск контейнера Nginx

Выполните команду [docker run](https://docs.docker.com/engine/reference/run/), чтобы запустить образ, извлеченный из реестра:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Перейдите по адресу [http://localhost:8080](http://localhost:8080), чтобы просмотреть запущенный контейнер.

Чтобы остановить и удалить контейнер, нажмите `Control`+`C`.

## <a name="remove-the-image-optional"></a>Удаление образа (необязательно)

Если вам больше не нужен образ Nginx, можно удалить его локально, выполнив команду [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Чтобы удалить образы из реестра контейнеров Azure, выполните команду Azure CLI [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete). Например, указанная ниже команда удаляет манифест, на который ссылается тег, любые связанные данные слоя и все другие теги, указывающие на манифест.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете основы, можно приступать к использованию реестра. Разверните образы контейнера из реестра в следующие службы:

* [Служба контейнеров Azure](../aks/tutorial-kubernetes-prepare-app.md)
* [Экземпляры контейнеров Azure](../container-instances/container-instances-tutorial-prepare-app.md);
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)
