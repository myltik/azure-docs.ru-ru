---
title: "Пулы общедоступных и частных агентов DC/OS для ACS | Документация Майкрософт"
description: "Как пулы общедоступных и частных агентов работают с кластером службы контейнеров Azure."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure"
ms.assetid: 36d657c9-8845-4bf7-bed2-088323b67406
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a6d9ab6d95de936e4f1d28d47d4e1d74c080bdae


---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Пулы агентов DC/OS для службы контейнеров Azure
Служба контейнеров Azure DC/OS распределяет агенты по общедоступным или частным пулам. Развертывание можно выполнить в любом пуле, что повлияет на доступность между компьютерами в службе контейнеров. Компьютеры можно подключить к Интернету (общедоступный пул) или только к внутренней сети (частный пул). В этой статье приводятся общие сведения о причинах использования общедоступных и частных пулов.

### <a name="private-agents"></a>Частные агенты
Частные узлы агентов работают в сети без глобальной маршрутизации. К такой сети можно обратиться только из зоны администратора или через пограничный маршрутизатор общедоступной зоны. По умолчанию DC/OS запускает приложения на частных узлах агента. Дополнительные сведения о сетевой безопасности можно получить в [документации по DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

### <a name="public-agents"></a>Общедоступные агенты
Общедоступные узлы агентов выполняют приложения и службы DC/OS в общедоступной сети. Дополнительные сведения о сетевой безопасности можно получить в [документации по DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

## <a name="using-agent-pools"></a>Использование пулов агентов
По умолчанию **Marathon** развертывает все новые приложения на *частных* узлах агентов. Приложение на *общедоступном* узле необходимо развернуть явно во время создания приложения. Перейдите на вкладку **Необязательно** и присвойте параметру **Accepted Resource Roles** (Принятые роли ресурсов) значение **slave_public**. Этот процесс описан [здесь](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) и в документации по [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [управлении контейнерами DC/OS](container-service-mesos-marathon-ui.md).

Узнайте, как [открыть брандмауэр](container-service-enable-public-access.md) , предоставляемый Azure, чтобы разрешить общий доступ к контейнеру DC/OS.




<!--HONumber=Nov16_HO3-->


