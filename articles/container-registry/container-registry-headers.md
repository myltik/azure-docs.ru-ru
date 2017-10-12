---
title: "Репозитории реестра контейнеров Azure | Документация Майкрософт"
description: "Использование репозиториев реестра контейнеров Azure для образов Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.openlocfilehash: 2090d4c951e2261529bf1b7b361510d5822060a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Репозитории реестра контейнеров Azure

Реестры контейнеров Azure совместимы со множеством служб и оркестраторов. Для упрощения отслеживания исходных служб и агентов, из которых используется ACR, мы начали использовать поле заголовка Docker в файле Docker.config.



## <a name="viewing-repositories-in-the-portal"></a>Просмотр репозиториев на портале

Заголовки ACR имеют следующий формат.
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Cloud: Azure, Azure Stack или другие облака для конкретной государственной организации или страны. Хотя в настоящее время Azure Stack и облака государственных организаций не поддерживаются, этот параметр позволяет будущую поддержку.
* Service: имя службы.
* Optionalservicename: необязательный параметр для служб с подчиненными службами или для указания номера SKU (пример: веб-приложения соответствуют Azure/app-service/web-apps).

Партнерским службам и оркестраторам рекомендуется использовать специальные значения заголовков для помощи в нашей телеметрии. Пользователи также могут изменять значение, переданное в заголовке, если возникнет такая необходимость.

Ниже приведены значения, которые партнерам рекомендуется использовать для заполнения поля "X-Meta-Source-Client".

| Имя службы              | Заголовок                                |
| ------------------------- | ------------------------------------- |
| Служба контейнеров Azure   | azure/compute/azure-container-service |
| Служба приложений — веб-приложения    | azure/app-service/web-apps            |
| Служба приложений — Logic Apps  | azure/app-service/logic-apps          |
| Пакетная служба                     | azure/compute/batch                   |
| Консоль облака             | azure/cloud-console                   |
| Functions                 | azure/compute/functions               |
| Интернет вещей — Центр  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| Машинное обучение          | azure/data/machile-learning           |
| Service Fabric            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>Дальнейшие действия
[Дополнительные сведения о реестрах, поддерживаемых службах и оркестраторах](container-registry-intro.md)
