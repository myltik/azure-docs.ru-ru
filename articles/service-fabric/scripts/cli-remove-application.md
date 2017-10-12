---
title: "Пример удаления сценария интерфейса командной строки Azure Service Fabric"
description: "Удаления приложения из кластера Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric"
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 0b9beb4801d3554d332925456f8df640bf3ffc0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Удаление приложения из кластера Service Fabric

Этот скрипт удаляет запущенный экземпляр приложения Service Fabric и отменяет регистрацию типа и версии приложения.  При удалении экземпляра приложения также удаляются все выполняющиеся экземпляры службы, связанные с этим приложением. Затем из хранилища образов удаляются файлы приложения. 

При необходимости установите [интерфейс командной строки Service Fabric](../service-fabric-cli.md).

## <a name="sample-script"></a>Пример скрипта

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [документации интерфейса командной строки Service Fabric](../service-fabric-cli.md).

Дополнительные примеры сценариев интерфейса командной строки Service Fabric для Azure Service Fabric см. в статье [Примеры сценариев Azure PowerShell](../samples-cli.md).
