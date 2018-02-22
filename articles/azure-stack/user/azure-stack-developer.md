---
title: "Разработка приложений для Azure Stack | Документация Майкрософт"
description: "Рекомендации по разработке в приложениях при создании прототипов приложений в Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 4b9e0f5a264296e59579539dd929f5d75a54b62a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2018
---
# <a name="develop-for-azure-stack"></a>Разработка для Azure Stack
Вы можете сразу приступить к разработке приложений даже при отсутствии доступа к среде Azure Stack. Так как Azure Stack предоставляет службы Microsoft Azure, выполняемые в вашем центре обработки данных, при разработке в среде Azure Stack вы можете использовать те же средства и процессы, что и для Azure.  Ознакомившись со следующими рекомендациями, вы сможете использовать Azure для эмуляции среды Azure Stack:

* В Azure можно создавать шаблоны Azure Resource Manager, которые также можно развертывать в Azure Stack.  См. [руководство по разработке шаблонов для обеспечения переносимости](azure-stack-develop-templates.md).
* Среды Azure и Azure Stack имеют различия в уровне доступности служб и возможностях управления версиями. Вы можете использовать [модуль политики Azure Stack](azure-stack-policy-module.md), чтобы ограничить доступность служб и типы ресурсов Azure для компонентов, доступных в Azure Stack. Ограничив доступность служб, вы поможете приложениям использовать службы, доступные в Azure Stack.
* [Шаблоны быстрого запуска для Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) — это общие примеры того, как создавать шаблоны, которые можно развертывать как в Azure, так и в Azure Stack.


