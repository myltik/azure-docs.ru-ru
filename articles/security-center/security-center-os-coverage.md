---
title: Поддерживаемые платформы в центре безопасности Azure | Документы Майкрософт
description: В этом документе содержится список операционных систем Windows и Linux, поддерживаемых в центре безопасности Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 86e8c1c8f0d8654c86b851e3ae7296476f38b94e
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34756907"
---
# <a name="supported-platforms-in-azure-security-center"></a>Поддерживаемые платформы в центре безопасности Azure
Мониторинг состояния работоспособности системы безопасности и рекомендации доступны для компьютеров и виртуальных машин, созданных посредством классической модели развертывания и модели развертывания Resource Manager.

> [!NOTE]
> Узнайте больше о [классической модели развертывания и модели развертывания Resource Manager](../azure-classic-rm.md) для ресурсов Azure.
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Поддерживаемые платформы для компьютеров и виртуальных машин Windows
Поддерживаемые операционные системы Windows:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Поддерживаемые платформы для компьютеров и виртуальных машин Linux
Поддерживаемые операционные системы Linux:

* Ubuntu версии 12.04, 14.04, 16.04, 16.10.
* Debian версии 7, 8.
* CentOS версии 6.\*, 7.*.
* Red Hat Enterprise Linux (RHEL) версии 6.\*, 7.*.
* SUSE Linux Enterprise Server (SLES) версии 11 с пакетом обновления 4 или выше, 12.*
* Oracle Linux версии 6.\*, 7.*.

> [!NOTE]
> Аналитика поведения виртуальной машины еще не доступна для операционных систем Linux.
>
>

## <a name="vms-and-cloud-services"></a>Виртуальные машины и облачные службы
Также поддерживаются виртуальные машины, запущенные в облачной службе. Мониторинг выполняется только для облачных служб и рабочих ролей, запущенных в слотах рабочей среды. Дополнительные сведения об облачной службе см. в разделе [Обзор облачных служб](../cloud-services/cloud-services-choose-me.md).

## <a name="next-steps"></a>Дополнительная информация

- [Руководство по планированию использования центра безопасности Azure и работе в нем](security-center-planning-and-operations-guide.md) — узнайте, как планировать работу с центром безопасности Azure, и получите рекомендации по переходу к его использованию.
- [Типы оповещений системы безопасности в центре безопасности Azure](security-center-alerts-type.md#virtual-machine-behavioral-analysis) — дополнительные сведения об анализе поведения виртуальных машин и анализе памяти аварийного дампа в центре безопасности.
- [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
- [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — в этом блоге можно найти публикации, посвященные безопасности и соответствию требованиям в Azure.
