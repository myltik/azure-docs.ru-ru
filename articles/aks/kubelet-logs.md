---
title: Получение журналов kubelet из узлов кластера Службы контейнеров Azure (AKS)
description: Получение журналов kubelet из узлов кластера Службы контейнеров Azure (AKS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 56e20a9f9d17eac01e6f85007db41dcc417f83e4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
---
# <a name="get-kubelet-logs-from-azure-container-service-aks-cluster-nodes"></a>Получение журналов kubelet из узлов кластера Службы контейнеров Azure (AKS)

В некоторых случаях для устранения неполадок требуется получить журналы kubelet из узла контейнера службы Azure (AKS). В этом документе описан один из вариантов получения этих журналов.

## <a name="create-an-ssh-connection"></a>Создание SSH-подключения

Сначала создайте SSH-подключение к узлу, из которого нужно получить журналы kubelet. Эта операция опесана в руководстве по [входу через SSH в узлы кластера Службы контейнеров Azure (AKS)][aks-ssh].

## <a name="get-kubelet-logs"></a>Получение журналов kubelet

Подключившись к узлу, используйте следующую команду для получения журналов kubelet.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

Пример выходных данных:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md