---
title: Развертывание контейнеров с помощью Helm в Kubernetes в Azure
description: Использование средства упаковки Helm для развертывания контейнеров в кластере Kubernetes в AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c46bd64b3fec06e4ba4050542f27ba3e70862e45
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Использование Helm со службой Azure Kubernetes

[Helm][helm] — это средство упаковки с открытым кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом. Аналогично диспетчерам пакетов Linux, таких как *APT* и *Yum*, Helm используется для управления чартами Kubernetes, представляющими собой пакеты предварительно настроенных ресурсов Kubernetes.

В этом документе представлены пошаговые инструкции по настройке и использованию Helm в кластере Kubernetes в AKS.

## <a name="before-you-begin"></a>Перед началом работы

В действиях, описанных в этом документе, предполагается, что кластер AKS создан и с ним установлено соединение kubectl. Если вам требуются эти компоненты, см. статью [Развертывание кластера Службы контейнеров Azure (AKS)][aks-quickstart].

## <a name="install-helm-cli"></a>Установка интерфейса командной строки Helm

Интерфейс командной строки Helm — это клиент, который выполняется в системе разработки и позволяет запускать и останавливать приложения с помощью чартов Helm, а также управлять ими.

Если вы используете Azure CloudShell, то интерфейс командной строки Helm уже установлен. Чтобы установить интерфейс командной строки Helm на компьютере Mac, выполните команду `brew`. Дополнительные параметры установки см. в статье об [установке Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Выходные данные:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Настройка Helm

Команда [helm init][helm-init] используется для установки компонентов Helm в кластере Kubernetes и выполнения настроек на стороне клиента. Выполните следующую команду, чтобы установить Helm в кластере AKS и настроить клиент Helm.

```azurecli-interactive
helm init
```

Выходные данные:

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
Happy Helming!
```

## <a name="find-helm-charts"></a>Поиск чартов Helm

Чарты Helm используются для развертывания приложений в кластере Kubernetes. Чтобы найти предварительно созданные чарты Helm, используйте команду [helm search][helm-search].

```azurecli-interactive
helm search
```

Выходные данные выглядят примерно следующим образом, но с дополнительными чартами.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Чтобы обновить список чартов, используйте команду [helm repo update][helm-repo-update].

```azurecli-interactive
helm repo update
```

Выходные данные:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Выполнение чартов Helm

Чтобы развернуть входящий контроллер NGINX, используйте команду [helm install][helm-install].

```azurecli-interactive
helm install stable/nginx-ingress
```

Вывод выглядит следующим образом, но включает дополнительные сведения, например инструкции по использованию развертывания Kubernetes.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Дополнительные сведения об использовании входящего контроллера NGINX с Kubernetes см. в [этой статье][nginx-ingress].

## <a name="list-helm-charts"></a>Список чартов Helm

Чтобы просмотреть список чартов, установленных в кластере, используйте команду [helm list][helm-list].

```azurecli-interactive
helm list
```

Выходные данные:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управлении чартами Kubernetes см. в документации по Helm.

> [!div class="nextstepaction"]
> [Документация по Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://github.com/kubernetes/helm/blob/master/docs/index.md
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md