---
title: "Подготовка цели (репликация виртуальных машин VMware в Azure) | Документация Майкрософт"
description: "В этой статье описывается, как подготовить среду Azure к запуску репликации виртуальных машин VMware в Azure."
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
ms.date: 5/31/2017
ms.author: bsiva
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: c84a775564769ddc796aa9d75add019ef1003175
ms.contentlocale: ru-ru
ms.lasthandoff: 06/15/2017

---

# <a name="prepare-target-vmware-to-azure"></a>Подготовка цели (репликация виртуальных машин VMware в Azure)
> [!div class="op_single_selector"]
> * [VMware в VMware](./site-recovery-prepare-target-vmware-to-azure.md)
> * [Из физического расположения в Azure](./site-recovery-prepare-target-physical-to-azure.md)

В этой статье описывается, как подготовить среду Azure к запуску репликации виртуальных машин VMware в Azure.

## <a name="prerequisites"></a>Предварительные требования

В данной статье предполагается, что выполнены следующие условия.
- Вы создали хранилище служб восстановления для защиты виртуальных машин VMware. Хранилище служб восстановления можно создать на [портале Azure](http://portal.azure.com "Портал Azure").
- Вы [настроили локальную среду](./site-recovery-set-up-vmware-to-azure.md) для репликации виртуальных машин VMware в Azure.

## <a name="prepare-target"></a>Подготовка цели

**Выбрав цель защиты** и **подготовив источник**, следует перейти к **настройке цели**.

![Подготовка цели](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **Подписка:** из раскрывающегося меню выберите подписку, в которую требуется реплицировать виртуальные машины.
2. **Модель развертывания:** выберите модель развертывания (классическую модель или модель Resource Manager).

После выбора модели развертывания выполняется проверка наличия как минимум одной совместимой учетной записи хранения и виртуальной сети в целевой подписке, выбранной для репликации и отработки отказа виртуальных машин.

После успешного завершения проверки нажмите кнопку "OK", чтобы перейти к следующему шагу.

Если совместимая учетная запись хранилища Resource Manager или виртуальная сеть отсутствует, либо вы хотите их добавить, вы можете это сделать, нажав кнопку **+ Storage Account** (+ Учетная запись хранения) или **+ Network** (+ Сеть) в верхней части колонки.

## <a name="next-steps"></a>Дальнейшие действия
[Настройка параметров репликации](./site-recovery-setup-replication-settings-vmware.md).

