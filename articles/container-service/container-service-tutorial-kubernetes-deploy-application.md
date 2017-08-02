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
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 94e1eacd6864a7fcaf753a11e855f6e69aee0f98
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017

---

# <a name="run-applications-in-kubernetes"></a>Запуск приложений в Kubernetes

В этом руководстве пример приложения развертывается в кластере Kubernetes. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * введение в объекты Kubernetes;
> * скачивание файлов манифестов Kubernetes;
> * выполнение приложения в Kubernetes;
> * Тестирование приложения

В следующих руководствах это приложение масштабируется, обновляется и отслеживается кластером Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образы контейнеров, образы были отправлены в реестр контейнеров Azure и был создан кластер Kubernetes. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

Для изучения данного руководства как минимум необходим кластер Kubernetes.

## <a name="kubernetes-objects"></a>Объекты Kubernetes

При развертывании контейнерного приложения в Kubernetes создается множество разных объектов Kubernetes. Каждый объект представляет требуемое состояние для кластера. Например, простое приложение может содержать pod, который объединяет тесно связанные контейнеры, постоянный том, представляющий собой часть сетевого хранилища, и развертывание, которое управляет состоянием приложения. 

Дополнительные сведения о всех объектах Kubernetes см. в разделе [Kubernetes Concepts](https://kubernetes.io/docs/concepts/) (Основные понятия Kubernetes) на сайте kubernetes.io.

## <a name="get-manifest-files"></a>Получение файлов манифестов

В этом руководстве Kubernetes объекты развертываются с помощью манифестов Kubernetes. Манифест Kubernetes — это YAML-файл, содержащий инструкции по настройке объекта.

Файлы манифеста для каждого объекта в этом руководстве доступны в репозитории приложения Vote Azure, который был клонирован в предыдущем руководстве. Если вы этого еще не сделали, клонируйте репозиторий с помощью приведенной ниже команды. 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Файлы манифеста находятся в указанном ниже каталоге клонированного репозитория.

```bash
/azure-voting-app/kubernetes-manifests/
```

## <a name="run-application"></a>Запуск приложения

### <a name="storage-objects"></a>Объекты хранилища

Так как приложение Vote Azure содержит базу данных MySQL, необходимо сохранить файл базы данных на томе, который может совместно использоваться разными pod. В этой конфигурации, если pod MySQL создается заново, то файл базы данных остается без изменений.

Файл манифеста `storage-resources.yaml` создает [объект класса хранилища](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#storageclasses), который определяет, как и где создается постоянный том. Для Kubernetes доступно несколько подключаемых модулей томов. В этом случае используется подключаемый модуль [Azure disk](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#azure-disk). 

Также создается [утверждение постоянного тома](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims), которое настраивает часть хранилища (с помощью класса Storage) и назначает его pod.

Выполните следующую команду для создания объектов хранилища.

```bash
kubectl create -f storage-resources.yaml
```

Будет создан виртуальный диск, который будет подключен к полученному pod Kubernetes. Этот виртуальный диск создается автоматически в учетной записи хранения, находящейся в той же группе ресурсов, что и кластер Kubernetes, и с такой же конфигурацией, что и объект класса хранилища (Standard_LRS).

### <a name="secure-sensitive-values"></a>Защита конфиденциальных значений

[Секреты Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/) обеспечивают безопасное хранение конфиденциальных сведений. С помощью файла `pod-secrets.yaml` учетные данные базы данных Vote Azure хранятся в секрете. 

Выполните следующую команду для создания объектов секрета.

```bash
kubectl create -f pod-secrets.yaml
```

### <a name="create-deployments"></a>Создание развертываний

[Развертывание Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) управляет состоянием различных pod Kubernetes. Оно обеспечивает работу требуемого количества реплик, подключение томов и использование правильных образов контейнеров.

Файл манифеста `azure-vote-deployment.yaml` создает развертывание для внешнего интерфейса и серверной частей приложения Vote Azure.

#### <a name="update-image-names"></a>Обновление имен образов

Если для хранения образов используется реестр контейнеров Azure, то имена образов должны содержать префикс с именем сервера входа ACR.

Получите имя сервера входа ACR, выполнив команду [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Обновите имена образов контейнеров *azure-vote-front* и *azure-vote-back* в файле `azure-vote-deployment.yaml`.

Пример имени образа внешнего интерфейса:

```yaml
containers:
      - name: azure-vote-front
        image: <acrLoginServer>/azure-vote-front:v1
```

Пример имени образа серверной части:

```yaml
containers:
      - name: azure-vote-back
        image: <acrLoginServer>/azure-vote-front:v1
```

#### <a name="create-deployment-objects"></a>Создание объектов развертывания

Выполните команду [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) для запуска приложения Vote Azure.

```bash
kubectl create -f azure-vote-deployment.yaml
```

### <a name="expose-application"></a>Предоставление приложения

[Служба Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) определяет способ доступа к pod. При использовании приложения Vote Azure развертывание серверной части должно быть доступно во внутренней среде по имени развертывания. Развертывание интерфейсной части должно быть доступно через Интернет. Конфигурации служб приложения Vote Azure определены в файле манифеста `services.yaml`.

Выполните следующую команду для создания служб.

```bash
kubectl create -f services.yaml
```

## <a name="test-application"></a>Тестирование приложения

После создания всех объектов доступ к приложению может быть получен через внешний IP-адрес для службы azure-vote-front. Создание этой службы может занять несколько минут. Чтобы отслеживать процесс создания службы, выполните следующую команду. Когда для значения *EXTERNAL-IP* службы *azure-vote-front* вместо состояния *В ожидании* отобразится IP-адрес, приложение будет готово и доступно по внешнему IP-адресу.

```bash
kubectl get service -w
```

Выходные данные:

```bash
NAME               CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
azure-vote-back    10.0.77.30    <none>          3306/TCP       4m
azure-vote-front   10.0.120.96   40.71.227.124   80:31482/TCP   4m
kubernetes         10.0.0.1      <none>          443/TCP        7m
```

Когда служба будет готова, выполните команду `CTRL-C`, чтобы завершить процесс слежения kubectl.

Перейдите по возвращенному внешнему IP-адресу, чтобы увидеть приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-external.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве приложение Vote Azure было развернуто в кластере Kubernetes Службы контейнеров Azure. Были выполнены следующие задачи:  

> [!div class="checklist"]
> * введение в объекты Kubernetes;
> * скачивание файлов манифестов Kubernetes;
> * выполнение приложения в Kubernetes;
> * Тестирование приложения

Перейдите к следующему руководству, чтобы узнать о масштабировании приложения Kubernetes и базовой инфраструктуры Kubernetes. 

> [!div class="nextstepaction"]
> [Масштабирование pod и инфраструктуры Kubernetes](./container-service-tutorial-kubernetes-scale.md)
