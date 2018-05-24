---
title: Пример сценария для интерфейса командной строки Service Fabric. Обновление приложения в кластере
description: Пример сценария для интерфейса командной строки Service Fabric для установки новой версии приложения. Этот пример устанавливает новую версию развернутого приложения.
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: e14e65e365389b33891794a3f12b86b3a4705533
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204385"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Добавление сертификата приложения в кластер Service Fabric

Этот сценарий передает новую версию существующего приложения, а затем обновляет развернутое приложение с ее помощью.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в [документации интерфейса командной строки Service Fabric](../service-fabric-cli.md).

Дополнительные примеры сценариев интерфейса командной строки Service Fabric для Azure Service Fabric см. в статье [Примеры сценариев Azure PowerShell](../samples-cli.md).
