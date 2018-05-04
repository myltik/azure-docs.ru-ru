---
title: Просмотр журналов контейнеров в Azure Service Fabric | Документация Майкрософт
description: Описывает способы просмотра журналов контейнеров в запущенных службах контейнеров Service Fabric с помощью Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Просмотр журналов службы контейнеров Service Fabric
Azure Service Fabric — это оркестратор контейнеров, который поддерживает [Linux-контейнеры и Windows-контейнеры](service-fabric-containers-overview.md).  В этой статье описывается, как просматривать журналы контейнеров работающей службы контейнеров, чтобы проводить диагностику и устранение проблем.

## <a name="access-container-logs"></a>Доступ к журналам контейнеров
Доступ к журналам контейнеров можно получить с помощью [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Откройте Service Fabric Explorer в веб-браузере из конечной точки управления кластера, перейдя на [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Журналы контейнеров расположены на узле кластера, на котором выполняется экземпляр службы контейнеров. Например, чтобы просмотреть журналы контейнера интерфейсной части [веб-приложения для голосования в Linux](service-fabric-quickstart-containers-linux.md). В представлении в виде дерева разверните ветку **Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront**.  Затем разверните раздел (здесь это d1aa737e-f22a-e347-be16-eec90be24bc1) и убедитесь, что контейнер запущен на узле кластера *_lnxvm_0*.

В представлении в виде дерева найдите пакет кода на узле *_lnxvm_0*, развернув ветку **Nodes**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Code Packages**>**code**.  Чтобы просмотреть журналы контейнеров, выберите элемент **Журналы контейнеров**.

![Платформа Service Fabric][Image1]


## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь с [Руководством по созданию контейнерных Linux-приложений](service-fabric-tutorial-create-container-images.md).
- Узнать больше о [Service Fabric и контейнерах](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
