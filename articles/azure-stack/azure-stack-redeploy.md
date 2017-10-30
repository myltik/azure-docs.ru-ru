---
title: "Повторное развертывание Azure Stack | Документация Майкрософт"
description: "Повторное развертывание Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Повторное развертывание Azure Stack
Чтобы повторно развернуть Azure Stack, необходимо выполнить описанную ниже процедуру с самого начала.

## <a name="steps-to-redeploy-azure-stack"></a>Действия по повторному развертыванию Azure Stack
1. На узле пакета средств разработки откройте консоль PowerShell с повышенными привилегиями, перейдите к скрипту asdk-installer.ps1, запустите его и нажмите кнопку **Перезапустить**.
2. Выберите основную операционную систему (не **Azure Stack**) и нажмите кнопку **Далее**.
3. Перезапустив узел пакета средств разработки, удалите файл CloudBuilder.vhdx, который использовался при предыдущем развертывании.
4. [Разверните пакет средств разработки](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Дальнейшие действия
[Подключение к Azure Stack](azure-stack-connect-azure-stack.md)

