---
title: 'Руководство по Службе контейнеров Azure: развертывание приложения'
description: 'Руководство по Службе контейнеров Azure: развертывание приложения'
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f8f626143e74d65fa9d4e37e1e2bfda37501f102
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162778"
---
# <a name="run-applications-in-kubernetes"></a>Запуск приложений в Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

В этом руководстве (часть 4 из 7) выполняется развертывание примера приложения в кластер Kubernetes. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * обновление файлов манифестов Kubernetes;
> * выполнение приложения в Kubernetes;
> * Тестирование приложения

В последующих руководствах описывается, как развернуть и обновить приложение, а также настроить Log Analytics для мониторинга кластера Kubernetes.

Для работы с этим руководством требуется понимание основных концепций Kubernetes. Подробные сведения см. в [документации по Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение упаковывалось в образ контейнера, далее этот образ отправлялся в реестр контейнеров Azure, после чего создавался кластер Kubernetes. 

Для работы с этим руководством необходимо предварительно создать файл манифеста Kubernetes `azure-vote-all-in-one-redis.yml`. Этот файл был скачан вместе с исходным кодом приложения в предыдущем руководстве. Проверьте, клонировали ли вы репозиторий и изменили ли каталоги на клонированный репозиторий.

Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Обновление файла манифеста

В этом руководстве для хранения образа контейнера использовался реестр контейнеров Azure (ACR). Перед запуском приложения необходимо обновить имя сервера входа ACR в файле манифеста Kubernetes.

Получите имя сервера входа ACR, выполнив команду [az acr list](/cli/azure/acr#az_acr_list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Предварительно созданный файл манифеста содержит имя сервера входа `microsoft`. Откройте этот файл в любом текстовом редакторе. В этом примере файл открыт в `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Замените `microsoft` именем сервера входа ACR. Это значение можно найти в строке **47** файла манифеста.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Сохраните и закройте файл.

## <a name="deploy-application"></a>Развертывание приложения

Используйте команду [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create), чтобы запустить приложение. Эта команда анализирует файл манифеста и создает заданные объекты Kubernetes.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Выходные данные:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Тестирование приложения

Создается [служба Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/), которая открывает доступ к приложению через Интернет. Это может занять несколько минут. 

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service](https://review.docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) с аргументом `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Изначально для параметра **EXTERNAL-IP** службы `azure-vote-front` отображается состояние `pending`. Как только для параметра "EXTERNAL-IP" состояние `pending` изменится на `IP address`, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Чтобы увидеть приложение, откройте в браузере внешний IP-адрес.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве приложение Vote Azure было развернуто в кластере Kubernetes Службы контейнеров Azure. Вы выполнили следующие задачи:  

> [!div class="checklist"]
> * скачивание файлов манифестов Kubernetes;
> * запуск приложения в Kubernetes;
> * тестирование приложения.

Перейдите к следующему руководству, чтобы узнать о масштабировании приложения Kubernetes и базовой инфраструктуры Kubernetes. 

> [!div class="nextstepaction"]
> [Масштабирование pod и инфраструктуры Kubernetes](./container-service-tutorial-kubernetes-scale.md)