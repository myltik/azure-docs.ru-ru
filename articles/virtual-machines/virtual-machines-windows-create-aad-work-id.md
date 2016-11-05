---
title: Создание рабочего или учебного удостоверения в Azure Active Directory | Microsoft Docs
description: Сведения о создании рабочих или учебных удостоверений в Azure Active Directory для использования с виртуальными машинами Windows.
services: virtual-machines-windows
documentationcenter: ''
author: squillace
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill

---
# Создание рабочего или учебного удостоверения в Azure Active Directory для использования с виртуальными машинами Windows
Если вы создали личную учетную запись Azure или имеете подписку на MSDN и создали учетную запись Azure для получения преимуществ кредитов MSDN Azure — при ее создании вы использовали удостоверение *учетной записи Майкрософт*. Для работы многих полезных функций Azure — одним из примеров являются [шаблоны групп ресурсов](../resource-group-overview.md) — требуется рабочая или школьная учетная запись (удостоверение, управляемое Azure Active Directory). Чтобы создать рабочую или учебную учетную запись, вы можете следовать инструкциям ниже. К счастью, одна из лучших особенностей личной учетной записи Azure в том, что она поставляется с доменом Azure Active Directory по умолчанию, который позволяет создать новую рабочую или школьную учетную запись и использовать ее с функциями Azure, которые ее требуют.

Однако последние изменения делают возможным управление подпиской с любым типом учетной записи Azure с помощью метода интерактивного входа `azure login`, который описан [здесь](../xplat-cli-connect.md). Вы можете использовать этот механизм или воспользоваться инструкциями, приведенными ниже. Вы также можете [создать рабочее или учебное удостоверение в Azure Active Directory для использования с виртуальными машинами Linux](virtual-machines-linux-create-aad-work-id.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[!INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]

<!---HONumber=AcomDC_0824_2016-->