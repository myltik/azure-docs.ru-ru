---
title: "Пример сценария для интерфейса командной строки Service Fabric. Вывод списка приложений в кластере"
description: "Пример сценария для интерфейса командной строки Service Fabric для вывода списка приложений, подготовленных в кластере Service Fabric."
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: 
ms.openlocfilehash: f764c36bcc4905224242cbdf1602d7482e52efac
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Вывод списка приложений, работающих в кластере Service Fabric

Этот пример сценария подключается к кластеру Service Fabric и выводит список всех подготовленных приложений в нем.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в [документации интерфейса командной строки Service Fabric](../service-fabric-cli.md).

Дополнительные примеры сценариев интерфейса командной строки Service Fabric для Azure Service Fabric см. в статье [Примеры сценариев Azure PowerShell](../samples-cli.md).
