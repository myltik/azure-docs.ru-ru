---
title: "Проверка предварительных требований для репликации из Hyper-V в Azure (без System Center VMM) с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье рассматриваются предварительные требования для настройки репликации, отработки отказа и восстановления локальных виртуальных машин Hyper-V в Azure с помощью Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 63ad1dcc5a069a9958e56c8260f9aa208fafc645
ms.contentlocale: ru-ru
ms.lasthandoff: 06/23/2017

---

# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>Шаг 2. Проверка предварительных требований для репликации из Hyper-V (без VMM) в Azure

В таблице кратко описаны предварительные требования.


**Предварительные требования** | **Дополнительные сведения** 
--- | --- 
**Таблицы Azure** | Ознакомьтесь с [требованиями Azure](site-recovery-prereq.md#azure-requirements).
**Локальные серверы** | [Ознакомьтесь](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager) с требованиями к локальным узлам Hyper-V.
**Локальные виртуальные машины Hyper-V** | Виртуальные машины, которые необходимо реплицировать, должны работать под управлением [поддерживаемой операционной системы](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) и соответствовать [предварительным требованиям Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL-адреса Azure** | Узлы Hyper-V должны иметь доступ к следующим URL-адресам:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> При использовании правил брандмауэра на основе IP-адресов убедитесь, что эти правила разрешают обмен данными с Azure.<br/></br> Разрешите доступ для [диапазонов IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) и использование порта HTTPS (443).<br/></br> Необходимо разрешить доступ для диапазонов IP-адресов региона Azure, в котором располагается ваша подписка, и региона "Западная часть США" (используется для контроля доступа и управления удостоверениями).



## <a name="next-steps"></a>Дальнейшие действия

- При выполнении полного развертывания перейдите к статье [Step 3: Plan capacity and scaling for physical server to Azure replication](hyper-v-site-walkthrough-capacity.md) (Шаг 3. Планирование производительности и масштабирования для репликации физического сервера в Azure).
- При выполнении простого тестового развертывания перейдите к разделу [Шаг 4. Планирование сетей для репликации из Hyper-V в Azure](hyper-v-site-walkthrough-network.md).

