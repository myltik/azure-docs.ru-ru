---
title: Руководство по Kubernetes в Azure. Развертывание приложения
description: Руководство по AKS.Развертывание приложения
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 79fc716f70fe0fb3273badd485e4c01ea5d04e1d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-run-applications-in-azure-container-service-aks"></a>Руководство. Запуск приложения в Службе контейнеров Azure (AKS)

В этом руководстве (часть 4 из 8) выполняется развертывание примера приложения в кластер Kubernetes. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * обновление файлов манифестов Kubernetes;
> * выполнение приложения в Kubernetes;
> * Тестирование приложения

В последующих руководствах описывается, как развернуть и обновить приложение, а также настроить Log Analytics для мониторинга кластера Kubernetes.

Для работы с этим руководством требуется понимание основных концепций Kubernetes. Подробные сведения см. в [документации по Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение упаковывалось в образ контейнера, далее этот образ отправлялся в реестр контейнеров Azure, после чего создавался кластер Kubernetes. 

Для работы с этим руководством необходимо предварительно создать файл манифеста Kubernetes `azure-vote-all-in-one-redis.yaml`. Этот файл был скачан вместе с исходным кодом приложения в предыдущем руководстве. Проверьте, клонировали ли вы репозиторий и изменили ли каталоги на клонированный репозиторий.

Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Prepare application for Azure Container Service (AKS)][aks-tutorial-prepare-app] (Подготовка приложений для службы контейнеров Azure (AKS)).

## <a name="update-manifest-file"></a>Обновление файла манифеста

В этом руководстве для хранения образа контейнера использовался реестр контейнеров Azure (ACR). Перед запуском приложения необходимо обновить имя сервера входа ACR в файле манифеста Kubernetes.

Получите имя сервера входа ACR, выполнив команду [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Предварительно созданный файл манифеста содержит имя сервера входа `microsoft`. Откройте этот файл в любом текстовом редакторе. В этом примере файл открыт в `nano`.

```console
nano azure-vote-all-in-one-redis.yaml
```

Замените `microsoft` именем сервера входа ACR. Это значение можно найти в строке **47** файла манифеста.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Приведенный выше код станет таким:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Сохраните и закройте файл.

## <a name="deploy-application"></a>Развертывание приложения

Используйте команду [kubectl create][kubectl-create], чтобы запустить приложение. Эта команда анализирует файл манифеста и создает заданные объекты Kubernetes.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Выходные данные:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Тестирование приложения

Создается [служба Kubernetes][kubernetes-service], которая открывает доступ к приложению через Интернет. Это может занять несколько минут. 

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service][kubectl-get] с аргументом `--watch`.

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

Если не удалось загрузить приложение, возможно, возникла проблема с авторизацией реестра образов.

Выполните приведенные ниже инструкции по [разрешению доступа при помощи секрета Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве приложение Azure для голосования было развернуто в кластере Kubernetes Службы контейнеров Azure. Вам предстоят следующие задачи:  

> [!div class="checklist"]
> * скачивание файлов манифестов Kubernetes;
> * запуск приложения в Kubernetes;
> * тестирование приложения.

Перейдите к следующему руководству, чтобы узнать о масштабировании приложения Kubernetes и базовой инфраструктуры Kubernetes. 

> [!div class="nextstepaction"]
> [Scale application in Azure Container Service (AKS)][aks-tutorial-scale] (Масштабирование приложений в службе контейнеров Azure (AKS))

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
