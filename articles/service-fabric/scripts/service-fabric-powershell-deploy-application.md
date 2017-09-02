---
title: "Пример сценария Azure PowerShell — развертывание приложения в кластер | Документы Майкрософт"
description: "Пример сценария Azure PowerShell — развертывание приложения из кластера Service Fabric."
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
ms.openlocfilehash: b9aa30e18f6997050f29792e53946d5ec747e9ff
ms.contentlocale: ru-ru
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Развертывание приложения в кластера Service Fabric

Этот пример сценария копирует пакет приложения в хранилище образов кластера, регистрирует тип приложения в кластере и создает экземпляр приложения с типом приложения.  Если в манифесте приложения для целевого типа приложения были определены используемые по умолчанию службы, то они также будут созданы. Измените параметры, если это необходимо. 

При необходимости установите модуль PowerShell ServiceFabric вместе с [пакетом SDK для Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[главный](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Развертывание приложения в кластере")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После запуска примера сценария можно использовать сценарий из раздела [Удаление приложения](service-fabric-powershell-remove-application.md) для удаления экземпляра приложения, отмены регистрация типа приложения и удаления пакета приложения из хранилища образов.

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Копирует пакет приложения в хранилище образов кластера.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Регистрирует тип и версию приложения в кластере. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Создает приложение на основе зарегистрированного типа приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Service Fabric PowerShell см. в [документации по Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Дополнительные примеры сценариев PowerShell для Azure Service Fabric см. в разделе [Примеры сценариев Azure PowerShell](../service-fabric-powershell-samples.md).

