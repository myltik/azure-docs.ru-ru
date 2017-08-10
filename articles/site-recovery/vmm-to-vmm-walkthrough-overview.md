---
title: "Репликация виртуальных машин Hyper-V на дополнительный сайт VMM с помощью Azure Site Recovery | Документация Майкрософт"
description: "Обзор репликации виртуальных машин Hyper-V на вторичный сайт VMM с помощью портала Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.contentlocale: ru-ru
ms.lasthandoff: 08/01/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Репликация виртуальных машин Hyper-V из облачных сред VMM в дополнительный ЦОД VMM

> [!div class="op_single_selector"]
> * [Портал Azure](site-recovery-vmm-to-vmm.md)
> * [Классический портал.](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell — Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

В этой статье приведены общие сведения о действиях, необходимых для репликации локальных виртуальных машин Hyper-V, управляемых в облаках System Center Virtual Machine Manager (VMM), на дополнительный сайт VMM с помощью службы [Azure Site Recovery](site-recovery-overview.md) на портале Azure.

Любые комментарии, которые возникнут после прочтения этой статьи, можно добавить в конце этой страницы или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Шаг 1. Ознакомление с архитектурой сценария

Перед началом развертывания ознакомьтесь с архитектурой сценария и убедитесь, что вам знакомы все компоненты, которые необходимо развернуть.

Перейдите к статье [Шаг 1. Обзор архитектуры для репликации Hyper-V на дополнительный сайт](vmm-to-vmm-walkthrough-architecture.md).

## <a name="step-2-review-prerequisites-and-limitations"></a>Шаг 2. Просмотр предварительных условий и ограничений

Убедитесь, что вы понимаете предварительные условия и ограничения для развертывания.

**Необходимые компоненты Azure.** Для координации репликации и управления ею требуются подписка Microsoft Azure и хранилище службы восстановления Azure.
**Локальные серверы VMM и узлы Hyper-V.** Убедитесь, что серверы VMM и узлы Hyper-V совместимы и подготовлены к развертыванию Site Recovery.

Перейдите к статье [Шаг 2. Проверка предварительных требований и ограничений для репликации виртуальной машины Hyper-V на дополнительный сайт VMM](vmm-to-vmm-walkthrough-prerequisites.md).

## <a name="step-3-plan-networking"></a>Шаг 3. Планирование сетей

Необходимо выполнить определенное планирование сетей, чтобы обеспечить возможность настройки сопоставления сетей виртуальных машин VMM при развертывании сценария.

Перейдите к статье [Шаг 3. Планирование сетей для репликации виртуальной машины Hyper-V на дополнительный сайт VMM](vmm-to-vmm-walkthrough-network.md).


## <a name="step-4-prepare-vmm-and-hyper-v"></a>Шаг 4. Подготовка VMM и Hyper-V

Подготовьте серверы VMM и узлы Hyper-V к развертыванию службы Site Recovery.

Перейдите к статье [Шаг 4. Подготовка серверов VMM и узлов Hyper-V к репликации Hyper-V в Azure](vmm-to-vmm-walkthrough-vmm-hyper-v.md).

## <a name="step-5-set-up-a-vault"></a>Шаг 5. Настройка хранилища

Настройте хранилище служб восстановления. которое содержит параметры конфигурации и управляет репликацией.

[Шаг 5. Создание хранилища для репликации Hyper-V на дополнительный сайт](vmm-to-vmm-walkthrough-create-vault.md).

## <a name="step-6-set-up-source-and-target-settings"></a>Шаг 6. Настройка исходной и целевой среды

Настройте исходное и целевое расположение для репликации VMM. Добавьте серверы VMM в хранилище и скачайте файлы установки для компонентов Site Recovery. Запустите поставщик Azure Site Recovery на сервере VMM. Программа установки устанавливает поставщик на сервере VMM и регистрирует сервер в хранилище. Установите агент служб восстановления Microsoft на каждом узле Hyper-V.

Перейдите к статье [Шаг 6. Настройка исходной и целевой среды](vmm-to-vmm-walkthrough-source-target.md).

## <a name="step-7-configure-network-mapping"></a>Шаг 7. Настройка сетевого сопоставления

Сопоставьте сети виртуальных машин VMM в исходном и целевом расположении. После отработки отказа виртуальные машины создаются в целевой сети, сопоставленной с сетью исходной виртуальной машины, в котором находится исходная виртуальная машина Hyper-V.

Перейдите к статье [Шаг 7. Настройка сетевого сопоставления для репликации Hyper-V (с VMM) в Azure](vmm-to-vmm-walkthrough-network-mapping.md).


## <a name="step-8-set-up-a-replication-policy"></a>Шаг 8. Настройка политики репликации

Укажите, как виртуальные машины будут реплицироваться между расположениями VMM.

Перейдите к статье [Шаг 8. Настройка политики репликации](vmm-to-vmm-walkthrough-replication.md).


## <a name="step-9-enable-replication-for-vms"></a>Шаг 9. Включение репликации для виртуальных машин

Выберите виртуальные машины для репликации. При включении виртуальной машины для репликации инициируется репликация на дополнительный сайт, после которой выполняется разностная репликация.

Перейдите к статье [Шаг 9. Включение репликации на дополнительный сайт для виртуальных машин Hyper-V](vmm-to-vmm-walkthrough-enable-replication.md).


## <a name="step-10-run-a-test-failover"></a>Шаг 10. Запуск тестовой отработки отказа

Запустите тестовую отработку отказа, чтобы убедиться в надлежащей работе всех компонентов.

Перейдите к статье [Шаг 10. Запуск тестовой отработки отказа для репликации Hyper-V на дополнительный сайт](vmm-to-vmm-walkthrough-test-failover.md).

