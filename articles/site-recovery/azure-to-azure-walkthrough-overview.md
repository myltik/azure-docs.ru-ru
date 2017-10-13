---
title: "Репликация виртуальных машин Azure между регионами Azure | Документация Майкрософт"
description: "В этой статье описан процесс репликации виртуальных машин Azure между регионами Azure с помощью службы Site Recovery Azure на портале Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Репликация виртуальных машин Azure между регионами с помощью Azure Site Recovery

>В этой статье представлен обзор процесса репликации виртуальных машин Azure из одного региона Azure на виртуальные машины Azure в другом регионе. 

>[!NOTE]
>
> Репликация виртуальных машин Azure сейчас доступна в режиме предварительной версии.

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="step-1-review-architecture"></a>Шаг 1. Общие сведения об архитектуре

Перед началом развертывания ознакомьтесь с архитектурой сценария и компонентами, которые необходимо развернуть.

Перейдите к статье [Шаг 1. Обзор архитектуры репликации физического сервера в Azure](azure-to-azure-walkthrough-architecture.md).


## <a name="step-2-review-prerequisites"></a>Шаг 2. Проверка предварительных требований

Убедитесь, что присутствуют все необходимые компоненты Azure, в том числе подписки, виртуальные сети, учетные записи хранения и виртуальные машины.

Перейдите к разделу [Шаг 2. Проверка предварительных требований для репликации из VMware в Azure](azure-to-azure-walkthrough-prerequisites.md).


## <a name="step-3-plan-networking"></a>Шаг 3. Планирование сетей

Убедитесь, что настроены исходящие подключения на виртуальных машинах Azure, которые нужно реплицировать, и подключения из локальных сетей.

Перейдите к статье [Шаг 4. Планирование сетей для репликации физического сервера в Azure](azure-to-azure-walkthrough-network.md).



## <a name="step-4-create-a-vault"></a>Шаг 4. Создание хранилища 

Для координации процесса репликации и управления им необходимо настроить хранилище Recovery Services и указать регион-источник.

См. [раздел о создании хранилища (шаг 4)](azure-to-azure-walkthrough-vault.md).


## <a name="step-5-enable-replication"></a>Шаг 5. Включение репликации


Чтобы включить репликацию, настройте параметры целевого расположения, настройте политику репликации и выберите виртуальные машины Azure, которую требуется реплицировать. После включения выполняется начальная репликация виртуальной машины.

Перейдите к статье, описывающей [включение репликации](azure-to-azure-walkthrough-enable-replication.md).


## <a name="step-6-run-a-test-failover"></a>Шаг 6. Выполнение тестовой отработки отказа

После завершения начальной репликации и запуска репликации разностных изменений можно выполнить тестовую отработку отказа, чтобы убедиться, что все работает правильно.

См. [раздел о выполнении тестовой отработки отказа (шаг 6)](azure-to-azure-walkthrough-test-failover.md).


