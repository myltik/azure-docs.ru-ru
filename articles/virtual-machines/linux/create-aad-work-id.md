---
title: "Создание рабочего или учебного удостоверения в AAD для Linux | Документация Майкрософт"
description: "Сведения о создании рабочих или учебных удостоверений в Azure Active Directory для использования с виртуальными машинами Linux."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: b0f86d77-c669-4aa1-a095-c2aa4d9105fe
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 4fdb72e3eec41d66f8561baf1755aa425ac278af
ms.lasthandoff: 04/03/2017


---
# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Создание рабочего или учебного удостоверения в Azure Active Directory для использования с виртуальными машинами Linux
Если вы создали личную учетную запись Azure или имеете подписку на MSDN и создали учетную запись Azure для получения преимуществ кредитов MSDN Azure — при ее создании вы использовали удостоверение *учетной записи Майкрософт*. Для работы многих полезных функций Azure — одним из примеров являются [шаблоны групп ресурсов](../../azure-resource-manager/resource-group-overview.md) — требуется рабочая или учебная учетная запись (удостоверение, управляемое Azure Active Directory). Чтобы создать рабочую или учебную учетную запись, вы можете следовать инструкциям ниже. К счастью, одна из лучших особенностей личной учетной записи Azure в том, что она поставляется с доменом Azure Active Directory по умолчанию, который позволяет создать новую рабочую или школьную учетную запись и использовать ее с функциями Azure, которые ее требуют.

Однако последние изменения делают возможным управление подпиской с любым типом учетной записи Azure с помощью метода интерактивного входа `azure login` , который описан [здесь](../../xplat-cli-connect.md). Вы можете использовать этот механизм или воспользоваться инструкциями, приведенными ниже. Вы также можете [создать рабочее или учебное удостоверение в Azure Active Directory для использования с виртуальными машинами Windows](../windows/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../../includes/virtual-machines-common-create-aad-work-id.md)]


