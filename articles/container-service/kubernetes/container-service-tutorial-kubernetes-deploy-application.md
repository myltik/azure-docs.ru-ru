---
title: "Руководство по Службе контейнеров Azure: развертывание приложения | Документация Майкрософт"
description: "Руководство по Службе контейнеров Azure: развертывание приложения"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>Запуск приложений в Kubernetes

В этом руководстве (часть 4 из 7) выполняется развертывание примера приложения в кластер Kubernetes. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * скачивание файлов манифестов Kubernetes;
> * выполнение приложения в Kubernetes;
> * Тестирование приложения

В последующих руководствах мы сегментируем это приложение, обновим его, а также настроим Operations Management Suite для отслеживания кластера Kubernetes.

Для работы с этим руководством требуется понимание основных концепций Kubernetes. Подробные сведения см. в [документации по Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение упаковывалось в образ контейнера, далее этот образ отправлялся в реестр контейнеров Azure, после чего создавался кластер Kubernetes. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

Для изучения данного руководства как минимум необходим кластер Kubernetes.

## <a name="get-manifest-file"></a>Получение файла манифеста

В этом руководстве [объекты Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) развертываются с помощью манифеста Kubernetes. Манифест Kubernetes — это файл формата YAML или JSON, содержащий инструкции по развертыванию и настройке объектов Kubernetes.

Файл манифеста приложения для этого руководства доступен в репозитории приложения Azure Vote, который был клонирован в предыдущем руководстве. Если вы этого еще не сделали, клонируйте репозиторий с помощью приведенной ниже команды. 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Файл манифеста находится в указанном ниже каталоге клонированного репозитория.

```bash
/azure-voting-app-redis/kubernetes-manifests/
```

## <a name="update-manifest-file"></a>Обновление файла манифеста

Если образы контейнеров хранятся в реестре контейнеров Azure, в манифест нужно добавить имя ACR loginServer.

Получите имя сервера входа ACR, выполнив команду [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Образец манифеста уже создан с именем репозитория *microsoft*. Откройте файл в любом текстовом редакторе и замените значение *microsoft* именем сервера для входа из соответствующего экземпляра ACR.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>Развертывание приложения

Используйте команду [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create), чтобы запустить приложение. Эта команда анализирует файл манифеста и создает заданные объекты Kubernetes.

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
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

Чтобы отслеживать ход выполнения, используйте команду [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) с аргументом `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Изначально для параметра **EXTERNAL-IP** службы *azure-vote-front* отображается значение *pending* (ожидание). Как только для параметра "ВНЕШНИЙ IP-АДРЕС" состояние *ожидания* изменится на *IP-адрес*, используйте команду `CTRL-C`, чтобы остановить процесс отслеживания kubectl.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Чтобы увидеть приложение, откройте в браузере внешний IP-адрес.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве приложение Vote Azure было развернуто в кластере Kubernetes Службы контейнеров Azure. Вы выполнили следующие задачи:  

> [!div class="checklist"]
> * скачивание файлов манифестов Kubernetes;
> * запуск приложения в Kubernetes;
> * тестирование приложения.

Перейдите к следующему руководству, чтобы узнать о масштабировании приложения Kubernetes и базовой инфраструктуры Kubernetes. 

> [!div class="nextstepaction"]
> [Масштабирование pod и инфраструктуры Kubernetes](./container-service-tutorial-kubernetes-scale.md)
