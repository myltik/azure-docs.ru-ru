---
title: "Пример сценария Azure PowerShell — добавление сертификата приложения в кластер | Документы Майкрософт"
description: "Пример сценария Azure PowerShell — добавление сертификата приложения в кластер Service Fabric."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 3212944a8bd882ced86e841944ac60bd1845e3d1
ms.contentlocale: ru-ru
ms.lasthandoff: 07/15/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Добавление сертификата приложения в кластер Service Fabric

В этом примере сценария создается самозаверяющий сертификат из указанного хранилища ключей Azure. Затем этот сертификат устанавливается на все узлы кластера Service Fabric. Сертификат также скачивается в локальную папку. Имя скачанного сертификата совпадает с именем сертификата в хранилище ключей. Измените параметры, если это необходимо.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве по Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[главный](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Добавление сертификата приложения в кластер")]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии используются следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | Добавляет новый сертификат приложения в масштабируемый набор виртуальных машин, входящих в состав кластера.  |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).

