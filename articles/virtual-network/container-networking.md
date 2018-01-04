---
title: "Виртуальная сеть Azure для контейнеров | Документы Microsoft"
description: "Дополнительные сведения о подключаемых модулей для кластеров Kubernetes CNI, позволяющий контейнеры для взаимодействия друг с другом и другие ресурсы, в виртуальной сети."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Сетевые подключения контейнеров

Azure предоставляет [подключаемый модуль интерфейса сети контейнера (CNI)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) , позволяющий развертывать и управлять свой кластер Kubernetes с собственного Azure сетевые возможности. Подключаемый модуль включена, по умолчанию при развертывании Kubernetes кластеры с [модуль службы контейнера Azure](https://github.com/Azure/acs-engine) (или модуль ACS).

## <a name="networking-capabilities"></a>Сетевые возможности

Контейнеры можно использовать широкий набор возможностей, которые предлагает виртуальной сети, такие как:
-   Создание отдельной виртуальной сети для кластера или развертывание кластера в существующей виртуальной сети. 
-   Каждый модуль в кластере получает IP-адрес в виртуальной сети и может напрямую взаимодействовать с других модулей в кластере, а также любой виртуальной машине в виртуальной сети. 
-   Типом pod могут использовать соединение для других модулей и виртуальными машинами в одноранговыми виртуальными сетями и локальными сетями через ExpressRoute "и" сеть сеть VPN-подключений. Локальные ресурсы могут взаимодействовать для модулей. 
-   Можно предоставлять службы Kubernetes к Интернету через подсистему балансировки нагрузки Azure.  
-   Обыкновенные в подсети, которая содержит конечные точки службы включена можно безопасно подключаться к службам Azure (хранилища и базы данных SQL, например).
-   Можно использовать определяемых пользователем маршрутов для маршрутизации трафика из модулей устройство виртуальной сети. 
-   Обыкновенные доступны открытые ресурсы в Интернете.
-   Можно назначить pod общедоступный IP-адрес, который будет связан с именем DNS.
 
## <a name="limits"></a>Ограничения
Вы можете развернуть до 4000 узлы в кластере Kubernetes и до 250 модулей на одном узле, общее ограничение в 16 000 модулей на кластер, при использовании подключаемого модуля.

## <a name="constraints"></a>Ограничения
- Подключаемый модуль не включен, при развертывании кластера Kubernetes с [контейнера службы Azure (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) кластер с Kubernetes.
- Не поддерживается собственный для Kubernetes политик сети, включая политики DNS или доступа.
- Подключаемый модуль не поддерживает сетевые политики pod.

## <a name="pricing"></a>Цены
Нет бесплатно с помощью подключаемого модуля CNI.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [развертывание кластера Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) в ваш [собственной виртуальной сети](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
