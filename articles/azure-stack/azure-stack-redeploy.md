---
title: "Повторное развертывание Azure Stack | Документация Майкрософт"
description: "Повторное развертывание Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Повторное развертывание Azure Stack
Если при развертывании Azure Stack поступило сообщение об ошибке, вы можете повторно запустить установку с помощью следующей команды PowerShell: `.\InstallAzureStackpoc.ps1 -rerun`. Эта команда позволяет перезапустить установку Azure Stack с момента ошибки, и вам не нужно будет начинать все с начала. Если ошибка установки происходит повторно, возможно, для ее устранения потребуется полностью повторить развертывание. 

Чтобы повторно развернуть Azure Stack, необходимо выполнить описанную ниже процедуру с самого начала.

## <a name="steps-to-redeploy-azure-stack"></a>Действия по повторному развертыванию Azure Stack
1. На узле пакета средств разработки откройте консоль PowerShell с повышенными привилегиями, перейдите к скрипту asdk-installer.ps1, запустите его и нажмите кнопку **Перезапустить**.
2. Выберите основную операционную систему (не **Azure Stack**) и нажмите кнопку **Далее**.
3. Перезапустив узел пакета средств разработки, удалите файл CloudBuilder.vhdx, который использовался при предыдущем развертывании.
4. [Разверните пакет средств разработки](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Дополнительная информация
[Подключение к Azure Stack](azure-stack-connect-azure-stack.md)

