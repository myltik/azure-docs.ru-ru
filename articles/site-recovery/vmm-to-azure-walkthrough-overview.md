---
title: "Репликация виртуальных машин Hyper-V в облаках VMM в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "Общие сведения о репликации виртуальных машин Hyper-V в облаках VMM в Azure с помощью службы Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Репликация виртуальных машин Hyper-V из облаков VMM в Azure с помощью Site Recovery на портале Azure
> [!div class="op_single_selector"]
> * [Портал Azure](site-recovery-vmm-to-azure.md)
> * [Классическая модель Azure](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell — Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell — классическая модель](site-recovery-deploy-with-powershell.md)


В этой статье приведены общие сведения о действиях, необходимых для репликации локальных виртуальных машин Hyper-V, управляемых в облаках System Center Virtual Machine Manager (VMM), в Azure, с помощью службы [Azure Site Recovery](site-recovery-overview.md) на портале Azure.

Любые комментарии, которые возникнут после прочтения этой статьи, можно добавить в конце этой страницы или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Шаг 1. Ознакомление с архитектурой сценария

Перед началом развертывания ознакомьтесь с архитектурой сценария и убедитесь, что вам знакомы все компоненты, которые необходимо развернуть.

Перейдите к статье [Шаг 1. Обзор архитектуры репликации физического сервера в Azure](vmm-to-azure-walkthrough-architecture.md).

## <a name="step-2-review-prerequisites-and-limitations"></a>Шаг 2. Просмотр предварительных условий и ограничений

Убедитесь, что вы понимаете предварительные условия и ограничения для развертывания.

**Предварительные требования Azure**: учетная запись Microsoft Azure, сети Azure и учетные записи хранения.
**Локальные серверы VMM и узлы Hyper-V.** Убедитесь, что серверы VMM и узлы Hyper-V совместимы и подготовлены к развертыванию Site Recovery.
**Реплицированные виртуальные машины.** Убедитесь, что виртуальные машины, которые требуется реплицировать, соответствуют требованиям Azure.

Перейдите к разделу [Шаг 2. Проверка предварительных требований для репликации из VMware в Azure](vmm-to-azure-walkthrough-prerequisites.md).

## <a name="step-3-plan-capacity"></a>Шаг 3. Планирование ресурсов

При выполнении полного развертывания необходимо выяснить, какие ресурсы требуются для репликации. Доступно несколько инструментов, которые помогут выполнить эту задачу. Если выполняется быстрая настройка для тестирования среды, то этот шаг можно пропустить.

Перейдите к статье [Шаг 3. Планирование ресурсов и масштабирования для репликации физического сервера в Azure](vmm-to-azure-walkthrough-capacity.md).

## <a name="step-4-plan-networking"></a>Шаг 4. Планирование сетей

Необходимо выполнить определенное планирование сетей, чтобы обеспечить возможность настройки сопоставления сетей при развертывании сценария, подключение виртуальных машин Azure к сетям после отработки отказа и правильность их IP-адресов.

Перейдите к статье [Шаг 4. Планирование сетей для репликации физического сервера в Azure](vmm-to-azure-walkthrough-network.md).


## <a name="step-5-prepare-azure-resources"></a>Шаг 5. Подготовка ресурсов Azure

Настройте учетную запись, сети и хранилище Azure. Это можно сделать при развертывании, но мы рекомендуем это сделать до начала процесса.

Перейдите к статье [Шаг 5. Подготовка ресурсов Azure для репликации Hyper-V в Azure](vmm-to-azure-walkthrough-prepare-azure.md).

## <a name="step-6-prepare-vmm-and-hyper-v"></a>Шаг 6. Подготовка VMM и Hyper-V

Подготовьте локальные серверы VMM и узлы Hyper-V к развертыванию службы Site Recovery.

Перейдите к статье [Шаг 6. Подготовка серверов VMM и узлов Hyper-V к репликации Hyper-V в Azure](vmm-to-azure-walkthrough-vmm-hyper-v.md).

## <a name="step-7-set-up-a-vault"></a>Шаг 7. Настройка хранилища

Настройте хранилище служб восстановления. которое содержит параметры конфигурации и управляет репликацией.

[Шаг 7. Настройка хранилища для репликации Hyper-V](vmm-to-azure-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Шаг 8. Настройка параметров исходной и целевой среды

Настройте исходное и целевое расположение для репликации. Добавьте сервер VMM в хранилище и скачайте файлы установки для компонентов Site Recovery. Запустите поставщик Azure Site Recovery на сервере VMM. Программа установки устанавливает поставщик на сервере VMM и регистрирует сервер в хранилище. Установите агент служб восстановления Microsoft на каждом узле Hyper-V.

Перейдите к статье [Шаг 8. Настройка исходных и целевых параметров для репликации Hyper-V (с VMM) в Azure](vmm-to-azure-walkthrough-source-target.md).

## <a name="step-9-configure-network-mapping"></a>Шаг 9. Настройка сетевого сопоставления

Сопоставьте локальные сети виртуальных машин VMM с виртуальными сетями Azure. После отработки отказа виртуальные машины Azure создаются в сети Azure, которая сопоставляется с локальной сетью виртуальной машины, в которой находится источник Hyper-V.

Перейдите к статье [Шаг 9. Настройка сетевого сопоставления для репликации Hyper-V (с VMM) в Azure](vmm-to-azure-walkthrough-network-mapping.md).


## <a name="step-10-set-up-a-replication-policy"></a>Шаг 10. Настройка политики репликации

Укажите, как локальные виртуальные машины будут реплицироваться в Azure.

Перейдите к статье [Шаг 10. Настройка политики репликации для репликации виртуальной машины Hyper-V (с VMM) в Azure](vmm-to-azure-walkthrough-replication.md).


## <a name="step-11-enable-replication-for-vms"></a>Шаг 11. Включение репликации для виртуальных машин

Выберите виртуальные машины для репликации. При включении виртуальной машины для репликации инициируется начальная репликация в Azure, после которой выполняется разностная репликация.

Перейдите к разделу [Шаг 11. Включение репликации](vmm-to-azure-walkthrough-enable-replication.md).


## <a name="step-12-run-a-test-failover"></a>Шаг 12. Запуск тестовой отработки отказа

Запустите тестовую отработку отказа, чтобы убедиться в надлежащей работе всех компонентов.

Перейдите к разделу [Шаг 12. Запуск тестовой отработки отказа](vmm-to-azure-walkthrough-test-failover.md).



