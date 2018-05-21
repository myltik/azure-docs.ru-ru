---
title: Использование статического IP-адреса с подсистемой балансировки нагрузки Службы Azure Kubernetes (AKS)
description: Использование статического IP-адреса с подсистемой балансировки нагрузки Службы Azure Kubernetes (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c250ef3520079f58eea2362212d861fdb134e1af
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Использование статического IP-адреса с подсистемой балансировки нагрузки Службы Azure Kubernetes (AKS)

В некоторых случаях, например при повторном создании подсистемы балансировки нагрузки Службы контейнеров Azure (AKS) или служб Kubernetes типа LoadBalancer, общедоступный IP-адрес службы Kubernetes может изменяться. В этом документе описывается настройка статического IP-адреса для служб Kubernetes.

## <a name="create-static-ip-address"></a>Создание статического IP-адреса

Создайте общедоступный статический IP-адрес для службы Kubernetes. Этот IP-адрес должен быть создан в группе ресурсов, которая была автоматически создана во время развертывания кластера. Сведения о различных группах ресурсов AKS и о том, как определить автоматически созданную группу ресурсов, приведены в разделе [часто задаваемых вопросов об AKS][aks-faq-resource-group].

Используйте команду [az network public ip create][az-network-public-ip-create], чтобы создать IP-адрес.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Запишите этот IP-адрес.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 При необходимости этот адрес можно получить с помощью команды [az network public-ip list][az-network-public-ip-list].

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Создание службы с IP-адресом

После подготовки статического IP-адреса можно будет создать службу Kubernetes со свойством `loadBalancerIP` и значением этого статического IP-адреса.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>Устранение неполадок

Если статический IP-адрес не был создан или был создана в неправильной группе ресурсов, то произойдет сбой создания службы. Для устранения неполадок верните события создания службы, выполнив команду [kubectl describe][kubectl-describe].

```azurecli-interactive
kubectl describe service azure-vote-front
```

```console
Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
