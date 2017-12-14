---
title: "Руководство по службе \"Экземпляры контейнеров Azure. Подготовка приложения"
description: "Подготовка приложения для развертывания экземпляров контейнера Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6a168b600833c7e4544da7a5f5f4b7a0af73e0b5
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Создание контейнера для развертывания в службе "Экземпляры контейнеров Azure"

Служба "Экземпляры контейнеров Azure" позволяет развертывать контейнеры Docker в инфраструктуре Azure без подготовки виртуальных машин или применения службы более высокого уровня. В этом руководстве мы создадим небольшое веб-приложение на Node.js и упакуем его в контейнер, который можно запустить в службе "Экземпляры контейнеров Azure". Мы рассмотрим:

> [!div class="checklist"]
> * Клонирование источника приложения из GitHub.
> * Создание образов контейнеров из источника приложения.
> * Тестирование образов в локальной среде Docker.

В последующих руководствах мы отправим образ в реестр контейнеров Azure, а затем развернем его в службе "Экземпляры контейнеров Azure".

## <a name="before-you-begin"></a>Перед началом работы

Для этого руководства требуется Azure CLI 2.0.21 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Для выполнения действий, описанных в этом руководстве, необходимо базовое понимание основных понятий Docker, таких как контейнеры, образы контейнеров и основные команды `docker`. При необходимости см. статью о [начале работы с Docker]( https://docs.docker.com/get-started/), чтобы ознакомиться с основами работы с контейнерами.

Для работы с этим руководством требуется среда разработки Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Таким образом, мы рекомендуем выполнить локальную установку среды разработки Azure CLI и Docker.

## <a name="get-application-code"></a>Получение кода приложения

Пример в этом руководстве включает простое веб-приложение, написанное на [Node.js](http://nodejs.org). Приложение работает как статическая HTML-страница и выглядит следующим образом:

![Приложение из руководства, отображающееся в браузере][aci-tutorial-app]

Скачайте пример с помощью следующей команды git:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Создание образа контейнера

Файл Dockerfile в примере репозитория демонстрирует, как создается контейнер. Он запускается из [официального образа Node.js][dockerhub-nodeimage] на основе [Alpine Linux](https://alpinelinux.org/), небольшого дистрибутива, который хорошо подходит для использования с контейнерами. Затем файлы приложения копируются в контейнер, при помощи диспетчера пакетов узла устанавливаются зависимости и, наконец, запускается приложение.

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Используйте команду `docker build`, чтобы создать образ контейнера, добавив тег *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Выходные данные команды `docker build` будут аналогичны следующим (сокращены для удобства).

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.2.0-alpine
8.2.0-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.2.0-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Используйте `docker images`, чтобы просмотреть созданный образ:

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Локальный запуск контейнера

Прежде чем развертывать контейнер в службе "Экземпляры контейнеров Azure", запустите его локально, чтобы убедиться, что он работает. При помощи параметра `-d` можно запустить контейнер в фоновом режиме, а при помощи `-p` — сопоставить произвольный порт на компьютере с портом 80 в контейнере.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Откройте в браузере страницу http://localhost:8080, чтобы убедиться, что контейнер запущен.

![Локальный запуск приложения в браузере][aci-tutorial-app-local]

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы создали образ контейнера, который можно развернуть в службе "Экземпляры контейнеров Azure". Были выполнены следующие действия:

> [!div class="checklist"]
> * клонирование источника приложения из GitHub;
> * создание образов контейнеров из источника приложения;
> * локальное тестирование контейнера.

Переходите к следующему руководству, чтобы узнать о хранении образов контейнеров в реестре контейнеров Azure.

> [!div class="nextstepaction"]
> [Развертывание реестра контейнеров Azure и его использование](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png