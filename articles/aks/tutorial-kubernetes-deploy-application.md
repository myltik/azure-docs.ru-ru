---
title: "Руководство по Kubernetes в Azure. Развертывание приложения"
description: "Руководство по AKS.Развертывание приложения"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3a6a75a324987b82a08219217407ad7ad14db9f8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Запуск приложения в Службе контейнеров Azure (AKS)

В этом руководстве (часть 4 из 8) выполняется развертывание примера приложения в кластер Kubernetes. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * обновление файлов манифестов Kubernetes;
> * выполнение приложения в Kubernetes;
> * Тестирование приложения

В последующих руководствах мы сегментируем это приложение, обновим его, а также настроим Operations Management Suite для отслеживания кластера Kubernetes.

Для работы с этим руководством требуется понимание основных концепций Kubernetes. Подробные сведения см. в [документации по Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение упаковывалось в образ контейнера, далее этот образ отправлялся в реестр контейнеров Azure, после чего создавался кластер Kubernetes. 

Для работы с этим руководством необходимо предварительно создать файл манифеста Kubernetes `azure-vote-all-in-one-redis.yml`. Этот файл был скачан вместе с исходным кодом приложения в предыдущем руководстве. Проверьте, клонировали ли вы репозиторий и изменили ли каталоги на клонированный репозиторий.

Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Обновление файла манифеста

В этом руководстве для хранения образа контейнера использовался реестр контейнеров Azure (ACR). Перед запуском приложения необходимо обновить имя сервера входа ACR в файле манифеста Kubernetes.

Получите имя сервера входа ACR, выполнив команду [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Предварительно созданный файл манифеста содержит имя сервера входа `microsoft`. Откройте этот файл в любом текстовом редакторе. В этом примере файл открыт в `vi`.

```console
vi azure-vote-all-in-one-redis.yml
```

Замените `microsoft` именем сервера входа ACR. Это значение можно найти в строке **47** файла манифеста.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Сохраните и закройте файл.

## <a name="deploy-application"></a>Развертывание приложения

Используйте команду [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create), чтобы запустить приложение. Эта команда анализирует файл манифеста и создает заданные объекты Kubernetes.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yml
```

Выходные данные:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Тестирование приложения

Создается [служба Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/), которая открывает доступ к приложению через Интернет. Это может занять несколько минут. 

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.7/#get) с аргументом `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

Изначально для параметра *ВНЕШНИЙ IP-АДРЕС* службы *azure-vote-front* отображается состояние *ожидания*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Как только *ВНЕШНИЙ IP-АДРЕС* изменится с состояния *ожидания* на *IP-адрес*, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания kubectl. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Чтобы увидеть приложение, откройте в браузере внешний IP-адрес.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве приложение Azure для голосования было развернуто в кластере Kubernetes Службы контейнеров Azure. Вам предстоят следующие задачи:  

> [!div class="checklist"]
> * скачивание файлов манифестов Kubernetes;
> * запуск приложения в Kubernetes;
> * тестирование приложения.

Перейдите к следующему руководству, чтобы узнать о масштабировании приложения Kubernetes и базовой инфраструктуры Kubernetes. 

> [!div class="nextstepaction"]
> [Масштабирование pod и инфраструктуры Kubernetes](./tutorial-kubernetes-scale.md)
