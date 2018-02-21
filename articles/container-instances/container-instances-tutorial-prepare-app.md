---
title: "Руководство по службе \"Экземпляры контейнеров Azure. Подготовка приложения"
description: "Руководство по службе \"Экземпляры контейнеров Azure\" —часть 1 из 3. Подготовка приложения для развертывания в службе \"Экземпляры контейнеров Azure\"."
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: fc16be80e776d1472be775fa32354ba157d16545
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Создание контейнера для развертывания в службе "Экземпляры контейнеров Azure"

Служба "Экземпляры контейнеров Azure" позволяет развертывать контейнеры Docker в инфраструктуре Azure без подготовки виртуальных машин или применения службы более высокого уровня. В этом руководстве мы создадим небольшое веб-приложение на Node.js и упакуем его в контейнер, который можно запустить в службе "Экземпляры контейнеров Azure".

В этой статье (первой части цикла) вы:

> [!div class="checklist"]
> * клонировать исходный код приложения из GitHub;
> * создадите образ контейнера из источника приложения;
> * протестируете образ в локальном окружении Docker.

В последующих руководствах мы отправим образ в реестр контейнеров Azure, а затем развернем его в службе "Экземпляры контейнеров Azure".

## <a name="before-you-begin"></a>Перед началом работы

Для этого руководства требуется Azure CLI 2.0.23 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

Для выполнения действий, описанных в этом руководстве, необходимо базовое понимание основных понятий Docker, таких как контейнеры, образы контейнеров и основные команды `docker`. При необходимости см. руководство по [началу работы с Docker][docker-get-started], чтобы ознакомиться с базовыми сведениями о контейнерах.

Для работы с этим руководством требуется среда разработки Docker, установленная локально. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе [Mac][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Для работы с этим руководством вам необходимо установить среду разработки Azure CLI и Docker на свой локальный компьютер.

## <a name="get-application-code"></a>Получение кода приложения

Пример в этом руководстве включает простое веб-приложение, написанное на [Node.js][nodejs]. Приложение работает как статическая HTML-страница и выглядит следующим образом:

![Приложение из руководства, отображающееся в браузере][aci-tutorial-app]

Скачайте пример с помощью следующей команды git:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Создание образа контейнера

Файл Dockerfile в примере репозитория демонстрирует, как создается контейнер. Он запускается из [официального образа Node.js][docker-hub-nodeimage] на основе [Alpine Linux][alpine-linux], небольшого дистрибутива, который хорошо подходит для использования с контейнерами. Затем файлы приложения копируются в контейнер, при помощи диспетчера пакетов узла устанавливаются зависимости и, наконец, запускается приложение.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Используйте команду [docker build][docker-build], чтобы создать образ контейнера, добавив тег *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Выходные данные команды [docker build][docker-build] будут выглядеть примерно так (сокращено для удобства):

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Выполните команду [docker images][docker-images], чтобы просмотреть созданный образ:

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

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве мы создали образ контейнера, который можно развернуть в службе "Экземпляры контейнеров Azure". Были выполнены следующие действия:

> [!div class="checklist"]
> * клонирование источника приложения из GitHub;
> * создание образов контейнеров из источника приложения;
> * локальное тестирование контейнера.

Переходите к следующему руководству, чтобы узнать о хранении образов контейнеров в реестре контейнеров Azure.

> [!div class="nextstepaction"]
> [Развертывание реестра контейнеров Azure и его использование](./container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
