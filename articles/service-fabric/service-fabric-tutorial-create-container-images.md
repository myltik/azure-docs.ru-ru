---
title: "Создание образов контейнеров для Azure Service Fabric | Документация Майкрософт"
description: "Узнайте, как создавать образы контейнеров для многоконтейнерного приложения Service Fabric."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, контейнеры, микрослужбы, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 08b3cc4a52c09ee03818b563794ef9b009d12ef4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-container-images-for-service-fabric"></a>Создание образов контейнеров для Service Fabric

Это руководство представляет собой первую часть цикла руководств, посвященного использованию контейнеров в кластере Service Fabric на платформе Linux. В этом руководстве выполняется подготовка многоконтейнерного приложения к использованию в Service Fabric. В последующих руководствах созданные образы используются как часть приложения Service Fabric. Из этого руководства вы узнаете, как выполнить следующие задачи: 

> [!div class="checklist"]
> * клонирование источника приложения из GitHub;  
> * создание образа контейнера из источника приложения;
> * развертывание экземпляра реестра контейнеров Azure;
> * добавление тегов к образу контейнера для реестра контейнеров Azure;
> * передача образа в реестр контейнеров Azure.

Из этого цикла руководств вы узнаете, как выполнять такие задачи: 

> [!div class="checklist"]
> * создание образов контейнеров для Service Fabric;
> * [создание и развертывание приложения Service Fabric с использованием контейнеров](service-fabric-tutorial-package-containers.md);
> * [отработка отказа и масштабирование в Service Fabric](service-fabric-tutorial-containers-failover.md).

## <a name="prerequisites"></a>Предварительные требования

- Настроенная среда разработки Linux для Service Fabric. Следуйте [этим](service-fabric-get-started-linux.md) инструкциям по настройке среды Linux. 
- Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 
- Кроме того, требуется наличие подписки Azure. Дополнительные сведения о бесплатной пробной версии можно получить [здесь](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Получение кода приложения

В этом руководстве используется пример приложения для голосования. Приложение состоит из веб-компонента интерфейсной части и серверного экземпляра Redis. Компоненты упаковываются в образы контейнеров. 

Используйте git, чтобы скачать копию приложения в среду разработки.

```bash
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

cd service-fabric-dotnet-containers/Linux/container-tutorial/
```

Каталог container-tutorial содержит папку azure-vote. Папка azure-vote содержит исходный код внешнего интерфейса и файл Dockerfile для создания внешнего интерфейса. Каталог container-tutorial содержит каталог redis с файлом Dockerfile для сборки образа Redis. Эти каталоги содержат необходимые ресурсы для данного руководства. 

## <a name="create-container-images"></a>Создание образов контейнеров

Выполните следующую команду в каталоге azure-vote, чтобы создать образ для компонента веб-интерфейса. Эта команда использует файл Dockerfile, расположенный в этом каталоге, для создания образа. 

```bash
docker build -t azure-vote-front .
```

Выполните приведенную ниже команду в каталоге redis, чтобы создать образ для серверной части Redis. Эта команда использует файл Dockerfile, расположенный в этом каталоге, для создания образа. 

```bash
docker build -t azure-vote-back .
```

После завершения выполните команду [docker images](https://docs.docker.com/engine/reference/commandline/images/), чтобы увидеть созданные образы.

```bash
docker images
```

Вы увидите, что были скачаны или созданы четыре образа. Образ *azure-vote-front* содержит приложение. Он является производным от образа *python* из Docker Hub. Образ Redis был скачан из Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

Сначала выполните команду [az login](/cli/azure/login), чтобы войти с учетной записью Azure. 

Затем введите команду [az account](/cli/azure/account#set), чтобы выбрать подписку для создания реестра контейнеров Azure. 

```bash
az account set --subscription <subscription_id>
```

При развертывании реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В этом примере создается группа ресурсов *myResourceGroup* в регионе *westus*. Выберите группу ресурсов в ближайшем к вам регионе. 

```bash
az group create --name myResourceGroup --location westus
```

Создайте реестр контейнеров Azure с помощью команды[az acr create](/cli/azure/acr#create). Имя контейнера реестра **должно быть уникальным**.

```bash
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

В остальной части этого руководства acrname будет заменять в примерах имя контейнера реестра.

## <a name="log-in-to-your-container-registry"></a>Вход в реестр контейнеров

Войдите в свой экземпляр реестра контейнеров Azure, прежде чем отправлять в него образы. Используйте команду [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login), чтобы выполнить операцию. Укажите уникальное имя реестра контейнеров, заданное при его создании.

```bash
az acr login --name <acrName>
```

После выполнения эта команда возвращает сообщение Login Succeeded (Вход выполнен).

## <a name="tag-container-images"></a>Присвоение тегов образам контейнеров

Каждый образ контейнера должен иметь тег с именем сервера входа (loginServer), указанным для реестра. Данный тег используется для маршрутизации при отправке образов контейнеров в реестр образов.

Чтобы просмотреть список сохраненных образов, используйте команду [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Чтобы получить имя loginServer, выполните следующую команду.

```bash
az acr show --name <acrName> --query loginServer --output table
```

Теперь пометьте образ *azure-vote-front* с помощью тега loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег обозначает номер версии образа.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Далее пометьте образ *azure-vote-back* с помощью тега loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег обозначает номер версии образа.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

После пометки выполните команду docker images для проверки операции.


Выходные данные:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                        latest              bf9a858a9269        22 minutes ago      107MB
<acrName>.azurecr.io/azure-vote-back    v1                  bf9a858a9269        22 minutes ago      107MB
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf        23 minutes ago      708MB
redis                                  latest              9813a7e8fcc0        2 days ago          107MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Отправка образов в реестр

Отправьте образ *azure-vote-front* в реестр. 

Используйте следующий пример, заменив в нем имя loginServer ACR именем loginServer своей среды.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Отправьте образ *azure-vote-back* в реестр. 

Используйте следующий пример, заменив в нем имя loginServer ACR именем loginServer своей среды.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

Выполнение команд docker push может занять несколько минут.

## <a name="list-images-in-registry"></a>Перечисление образов в реестре

Чтобы получить список образов, отправленных в реестр контейнеров Azure, выполните команду [az acr repository list](/cli/azure/acr/repository#list). Укажите в команде имя нужного экземпляра ACR.

```bash
az acr repository list --name <acrName> --output table
```

Выходные данные:

```bash
Result
----------------
azure-vote-back
azure-vote-front
```

По завершении работы с этим руководством образ контейнера будет сохранен в частном экземпляре реестра контейнеров Azure. В следующих руководствах мы развернем этот образ из ACR в кластер Service Fabric.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве приложение было извлечено из Github, а также были созданы образы контейнеров, которые затем были помещены в реестр. Были выполнены следующие действия:

> [!div class="checklist"]
> * клонирование источника приложения из GitHub;  
> * создание образа контейнера из источника приложения;
> * развертывание экземпляра реестра контейнеров Azure;
> * добавление тегов к образу контейнера для реестра контейнеров Azure;
> * передача образа в реестр контейнеров Azure.

Перейдите к следующему руководству, чтобы узнать об упаковке контейнеров в приложение Service Fabric с помощью Yeoman. 

> [!div class="nextstepaction"]
> [Упаковка и развертывание контейнеров в виде приложения Service Fabric](service-fabric-tutorial-package-containers.md)
