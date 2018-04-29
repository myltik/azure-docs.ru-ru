---
title: Руководство по реестру контейнеров Azure. Подготовка геореплицированного реестра контейнеров Azure
description: Создайте реестр контейнеров Azure, настройте георепликацию, подготовьте образ Docker и разверните его в реестре. Первая часть руководства из трех частей.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 2e91a92d34131d0b35cfb7b0bfdca99637924552
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Руководство. Подготовка геореплицированного реестра контейнеров Azure

Реестр контейнеров Azure — это частный реестр Docker, развернутый в Azure, который можно хранить недалеко от развертываний. Из этого набора из трех руководств вы узнаете, как с помощью георепликации развернуть веб-приложение ASP.NET Core, запущенное в контейнере Linux, на двух экземплярах [веб-приложений для контейнеров](../app-service/containers/index.yml). Вы узнаете, как автоматически развернуть образы для каждого экземпляра веб-приложения из ближайшего геореплицированного репозитория с помощью Azure.

В этой первой части руководства из трех частей вы узнаете, как:

> [!div class="checklist"]
> * создавать геореплицированный реестр контейнеров Azure;
> * клонировать исходный код приложения из GitHub;
> * создавать образ контейнера Docker из источника приложения;
> * отправлять образ контейнера в реестр.

В последующих руководствах вы развернете контейнер из частного реестра в веб-приложении, запущенном в двух регионах Azure. Затем вы обновите код в приложении, а также оба экземпляра веб-приложения с помощью одной команды `docker push` в реестре.

## <a name="before-you-begin"></a>Перед началом работы

Для этого руководства требуется Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

Для выполнения действий, описанных в этом руководстве, необходимо базовое понимание основных понятий Docker, таких как контейнеры, образы контейнеров и основные команды Docker. При необходимости см. статью о [начале работы с Docker]( https://docs.docker.com/get-started/), чтобы ознакомиться с основами работы с контейнерами.

Для работы с этим руководством требуется среда разработки Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Таким образом, мы рекомендуем выполнить локальную установку среды разработки Azure CLI и Docker.

> [!IMPORTANT]
> Функция георепликации реестра контейнеров Azure сейчас находится в **предварительной версии**. Предварительные версии предоставляются при условии, что вы соглашаетесь с [дополнительными условиями использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

Войдите на [портале Azure](http://portal.azure.com).

Последовательно выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров Azure**.

![Создание реестра контейнеров на портале Azure][tut-portal-01]

Настройте свой новый реестр, используя следующие параметры.

* **Registry name** (Имя реестра). Создайте глобально уникальное имя реестра в Azure, которое содержит от 5 до 50 буквенно-цифровых символов.
* **Группа ресурсов.** **Создайте новую** > `myResourceGroup`.
* **Расположение**`West US`.
* **Пользователь-администратор.** `Enable` (необходимо для веб-приложения для контейнеров, чтобы извлекать образы).
* **SKU.** `Premium` (необходимо для георепликации).

Выберите **Создать**, чтобы развернуть экземпляр записи контроля доступа (ACR).

![Создание реестра контейнеров на портале Azure][tut-portal-02]

В этом руководстве будет использоваться имя `<acrName>` как заполнитель выбранного **имени реестра контейнеров**.

> [!TIP]
> Так как обычно реестры контейнеров Azure — это долговременные ресурсы, которые используются на нескольких узлах контейнера, мы рекомендуем создать реестр в его собственной группе ресурсов. После настройки геореплицированных реестров и веб-перехватчиков эти дополнительные ресурсы помещаются в одну и ту же группу ресурсов.
>

## <a name="configure-geo-replication"></a>Настройка георепликации

Теперь, когда у вас есть реестр уровня "Премиум", можно настроить георепликацию. Веб-приложение, которое вы настроите в следующем руководстве для запуска в двух регионах, может извлекать образы контейнера из ближайшего реестра.

Перейдите в новый реестр контейнеров на портале Azure и в разделе **Службы** выберите **Replications** (Репликации).

![Репликации в реестре контейнера пользовательского интерфейса на портале Azure][tut-portal-03]

Отобразится карта, на которой показаны зеленые шестиугольники, отображающие регионы Azure, доступные для георепликации:

 ![Карта регионов на портале Azure][tut-map-01]

Реплицируйте свой реестр в регион "Восточная часть США". Для этого выберите его зеленый шестиугольник, а затем в разделе **Create replication** (Создать репликацию) нажмите кнопку **Создать**.

 ![Создание пользовательского интерфейса репликации на портале Azure][tut-portal-04]

По завершении репликации на портале отобразится состояние *Готово* для обоих регионов. С помощью кнопки **Обновить** можно обновить состояние репликации. Чтобы создать и синхронизировать реплики, может потребоваться несколько минут.

![Пользовательский интерфейс состояния репликации на портале Azure][tut-portal-05]

## <a name="container-registry-login"></a>Вход в реестр контейнеров

Теперь, после настройки георепликации, создайте образ контейнера и отправьте его в свой реестр. Сначала войдите в свой экземпляр ACR, прежде чем отправлять в него образы. С помощью [SKU уровня "Базовый", "Стандартный" и "Премиум"](container-registry-skus.md) можно выполнить аутентификацию, используя удостоверение Azure.

Выполните команду [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login), чтобы аутентифицировать и кэшировать учетные данные для вашего реестра. Замените `<acrName>` именем реестра, созданного на предыдущих шагах.

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает `Login Succeeded`.

## <a name="get-application-code"></a>Получение кода приложения

Пример в этом руководстве включает небольшое веб-приложение, созданное с помощью [ASP.NET Core](http://dot.net). Приложение обслуживает страницу HTML, отображающую регион, из которого был развернут образ с помощью реестра контейнеров Azure.

![Приложение из руководства, отображающееся в браузере][tut-app-01]

Выполните указанную ниже команду git, чтобы загрузить пример в локальный каталог. Или выполните команду `cd`, чтобы загрузить пример в обычный каталог.

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Обновление Dockerfile

Файл Dockerfile в примере репозитория демонстрирует, как создается контейнер. Он запускается из официального образа [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore), копирует файлы приложения в контейнер, устанавливает зависимости, компилирует выходные данные с помощью официального образа [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build) и, наконец, оптимизирует образ aspnetcore.

Dockerfile расположен в каталоге `./AcrHelloworld/Dockerfile` в клонированном источнике.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

Приложение в образе *acr-helloworld* пытается определить регион, из которого был развернут его контейнер, запросив информацию о сервере входа в реестр у DNS. Необходимо указать URL-адрес сервера входа в реестр в переменной среде `DOCKER_REGISTRY` в Dockerfile.

Сначала необходимо получить URL-адрес сервера входа в реестр, выполнив команду `az acr show`. Замените `<acrName>` именем реестра, созданного на предыдущих шагах.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Выходные данные:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Затем обновите строку `DOCKER_REGISTRY`, используя URL-адрес сервера входа в реестр. В этом примере мы обновим строку в соответствии с примером имени реестра *uniqueregistryname*:

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Создание образа контейнера

Теперь, когда вы обновили файл Dockerfile с помощью URL-адреса реестра, можно выполнить команду `docker build`, чтобы создать образ контейнера. Выполните указанную ниже команду, чтобы создать образ и пометить его URL-адресом частного реестра. Снова замените `<acrName>` именем своего реестра.

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Несколько строк выходных данных отображаются во время создания образа Docker (ниже показан усеченный пример).

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Выполните команду `docker images`, чтобы просмотреть созданный образ:

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Передача образа в реестр контейнеров Azure

Наконец, выполните команду `docker push`, чтобы отправить образ *acr-helloworld* в реестр. Замените `<acrName>` именем реестра.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Так как реестр настроен для георепликации, ваш образ будет автоматически реплицирован в регион *западной части США* и *восточной части США*. Для этого необходимо выполнить отдельную команду `docker push`.

Выходные данные:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали частный, геореплицированный реестр контейнеров, образ контейнера, а затем отправили образ в реестр. Следуя шагам в этом руководстве, вы:

> [!div class="checklist"]
> * создали геореплицированный реестр контейнеров Azure;
> * клонировали исходный код приложения из GitHub;
> * создали образ контейнера Docker из источника приложения;
> * отправили образ контейнера в реестр.

Перейдите к следующему руководству, чтобы ознакомиться с развертыванием контейнера в нескольких экземплярах службы "Веб-приложение для контейнеров" с использованием георепликации для локального обслуживания образов.

> [!div class="nextstepaction"]
> [Развертывание веб-приложения из реестра контейнеров Azure](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
