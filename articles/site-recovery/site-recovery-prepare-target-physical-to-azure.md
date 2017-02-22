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
ms.date: 2/11/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: b7f26df96ddedb579cf5d9d20fee6b2599e762e0
ms.openlocfilehash: 9dd4e9007da6cf276f976e2f3f25305286830544

---

# <a name="prepare-target-vmware-to-azure"></a>Подготовка цели (репликация виртуальных машин VMware в Azure)
> [!div class="op_single_selector"]
> * [Виртуальные машины VMware](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Физические серверы](./site-recovery-prepare-target-physical-to-azure.md)

В этой статье описывается, как подготовить среду Azure к запуску репликации&64;-разрядных физических серверов Windows или Linux в Azure.

## <a name="prerequisites"></a>Предварительные требования

В данной статье предполагается, что выполнены следующие условия.
- Вы создали хранилище служб восстановления для защиты физических серверов. Хранилище служб восстановления можно создать на [портале Azure](http://portal.azure.com "Портал Azure").
- Вы [настроили локальную среду](./site-recovery-set-up-physical-to-azure.md) для репликации физических серверов в Azure.

## <a name="prepare-target"></a>Подготовка цели

**Выбрав цель защиты** и **подготовив источник**, следует перейти к **настройке цели**.

![Подготовка цели](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **Подписка:** из раскрывающегося меню выберите подписку, в которую требуется реплицировать физические серверы.
2. **Модель развертывания:** выберите модель развертывания (классическую модель или модель Resource Manager).

После выбора модели развертывания выполняется проверка наличия как минимум одной совместимой учетной записи хранения и виртуальной сети в целевой подписке, выбранной для репликации и отработки отказа физических серверов.

После успешного завершения проверки нажмите кнопку "OK", чтобы перейти к следующему шагу.

Если совместимая учетная запись хранилища Resource Manager или виртуальная сеть отсутствует, либо вы хотите их добавить, вы можете это сделать, нажав кнопку **+ Storage Account** (+ Учетная запись хранения) или **+ Network** (+ Сеть) в верхней части колонки.

## <a name="next-steps"></a>Дальнейшие действия
[Настройка параметров репликации](./site-recovery-setup-replication-settings-vmware.md).



<!--HONumber=Feb17_HO2-->


