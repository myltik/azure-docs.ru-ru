---
title: Виртуальная сеть Azure для контейнеров | Документация Майкрософт
description: Сведения о подключаемом модуле CNI для кластеров Kubernetes, которые позволяют контейнерам взаимодействовать между собой и другими ресурсами в виртуальной сети.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2017
ms.locfileid: "26766404"
---
# <a name="container-networking"></a>Работа с контейнерами в сети

[Подключаемый модуль сетевого интерфейса работы с контейнерами (CNI)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) в Azure позволяет развертывать собственные кластеры Kubernetes со встроенной возможностью работы с сетью Azure и управлять ими. При развертывании кластеров Kubernetes с помощью [обработчика Службы контейнеров Azure](https://github.com/Azure/acs-engine) (обработчик ACS) этот подключаемый модуль включен по умолчанию.

## <a name="networking-capabilities"></a>Возможности работы с сетью

Контейнеры могут использовать широкий набор возможностей, предлагаемых виртуальной сетью. Ниже приведены их примеры.
-   Вы можете создать отдельную виртуальную сеть для кластера или развернуть его в имеющуюся виртуальную сеть. 
-   Каждая группа контейнеров pod в кластере получает IP-адрес из виртуальной сети и может напрямую взаимодействовать с другими группами pod в кластере, а также с любой виртуальной машиной в виртуальной сети. 
-   Группа контейнеров pod может подключаться к другим таким же группам и виртуальным машинам в одноранговых виртуальных сетях, а также к локальным сетям через ExpressRoute и VPN-подключения типа "сайт — сайт". Локальные ресурсы могут взаимодействовать с группами контейнеров pod. 
-   Вы можете предоставлять службы Kubernetes в Интернете через подсистему балансировки нагрузки Azure.  
-   Группы контейнеров pod с включенными конечными точками службы могут безопасно подключаться к службам Azure (например, к службе хранилища или базе данных SQL).
-   Трафик из групп контейнеров pod к виртуальным сетевым модулям можно маршрутизировать через определяемые пользователем маршруты. 
-   Группы контейнеров pod могут получать доступ к общедоступным ресурсам в Интернете.
-   Группе контейнеров pod можно присвоить общедоступный IP-адрес, который можно связать с DNS-именем.
 
## <a name="limits"></a>Ограничения
При использовании этого подключаемого модуля вы можете развернуть до 4000 узлов в кластере Kubernetes, до 250 групп контейнеров pod в узле, 16 000 групп контейнеров pod в кластере.

## <a name="constraints"></a>Ограничения
- При развертывании кластера Kubernetes со [Службой контейнеров Azure (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или кластера [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) с Kubernetes подключаемый модуль не включен.
- Встроенная поддержка политик сети Kubernetes, в том числе политик доступа или DNS, не реализована.
- Этот подключаемый модуль не поддерживает применение политик сети к отдельным группам контейнеров pod.

## <a name="pricing"></a>Цены
Плата за использование подключаемого модуля CNI не взимается.

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как [развернуть кластер Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) в [собственной виртуальной сети](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
