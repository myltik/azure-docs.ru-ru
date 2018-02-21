---
title: "Подготовка цели (репликация физических серверов в Azure) | Документация Майкрософт"
description: "В этой статье описывается, как подготовить среду Azure к запуску репликации физических серверов Windows или Linux в Azure."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: bsiva
ms.openlocfilehash: 2c5377f7193f8357a7e99ed1ef1a61b066b8ce5f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="prepare-target-vmware-to-azure"></a>Подготовка цели (репликация виртуальных машин VMware в Azure)
> [!div class="op_single_selector"]
> * [VMware в VMware](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Из физического расположения в Azure](./site-recovery-prepare-target-physical-to-azure.md)

В этой статье описывается, как подготовить среду Azure к запуску репликации 64-разрядных физических серверов Windows или Linux в Azure.

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается:
- Вы создали хранилище служб восстановления для защиты физических серверов. Хранилище служб восстановления можно создать на [портале Azure](http://portal.azure.com "Портал Azure").
- Вы [настроили локальную среду](./site-recovery-set-up-physical-to-azure.md) для репликации физических серверов в Azure.

## <a name="prepare-target"></a>Подготовка цели

**Выбрав цель защиты** и **подготовив источник**, следует перейти к **настройке цели**.

![Подготовка цели](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Подписка:** из раскрывающегося меню выберите подписку, в которую требуется реплицировать физические серверы.
2. **Модель развертывания:** выберите модель развертывания (классическую модель или модель Resource Manager).

После выбора модели развертывания выполняется проверка наличия как минимум одной совместимой учетной записи хранения и виртуальной сети в целевой подписке, выбранной для репликации и отработки отказа физических серверов.

После успешного завершения проверки нажмите кнопку "OK", чтобы перейти к следующему шагу.

Если у вас нет совместимых учетных записей хранения диспетчера ресурсов или виртуальной сети, их можно создать, нажав кнопку**+ Storage Account** (+ Учетная запись хранения) или кнопку **+ Network** (+ Сеть) в верхней части страницы.

## <a name="next-steps"></a>Дополнительная информация
[Настройка параметров репликации](./site-recovery-setup-replication-settings-vmware.md).
