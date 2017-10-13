---
title: "Репликация виртуальных машин Hyper-V в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этот статье описано, как управлять репликацией, отработкой отказа и восстановлением локальных виртуальных машин Hyper-V в Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>Репликация виртуальных машин Hyper-V (без VMM) в Azure 

> [!div class="op_single_selector"]
> * [Портал Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Классическая модель Azure](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell — Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

В этой статье кратко описаны шаги для репликации локальных виртуальных машин Hyper-V в Azure с помощью [Azure Site Recovery](site-recovery-overview.md) на портале Azure. В этом развертывании Hyper-V виртуальные машины не передаются под управление System Center Virtual Machine Manager (VMM).


Комментарии или вопросы технического характера можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-architecture-and-prerequisites"></a>Шаг 1. Проверка архитектуры и предварительных требований

Перед началом развертывания ознакомьтесь с архитектурой сценария и убедитесь, что вам знакомы все компоненты, которые необходимо развернуть.

Перейдите к статье [Шаг 1. Обзор архитектуры репликации физического сервера в Azure](hyper-v-site-walkthrough-architecture.md).


## <a name="step-2-review-prerequisites"></a>Шаг 2. Проверка предварительных требований

Убедитесь, что предварительные требования выполняются для каждого компонента развертывания.

- **Предварительные требования Azure**: учетная запись Microsoft Azure, сети Azure и учетные записи хранения.
- **Предварительные требования локальной среды Hyper-V**: убедитесь, что узлы Hyper-V подготовлены к развертыванию Site Recovery.
- **Реплицированные виртуальные машины**: виртуальные машины, которые требуется реплицировать, должны соответствовать требованиям Azure.

Перейдите к разделу [Шаг 2. Проверка предварительных требований для репликации из VMware в Azure](hyper-v-site-walkthrough-prerequisites.md).

## <a name="step-3-plan-capacity"></a>Шаг 3. Планирование ресурсов

При выполнении полного развертывания необходимо выяснить, какие ресурсы требуются для репликации. Доступно несколько инструментов, которые помогут выполнить эту задачу. Перейдите к шагу 2. Если выполняется быстрая настройка для тестирования среды, то этот шаг можно пропустить.

Перейдите к статье [Шаг 3. Планирование ресурсов и масштабирования для репликации физического сервера в Azure](hyper-v-site-walkthrough-capacity.md).

## <a name="step-4-plan-networking"></a>Шаг 4. Планирование сетей

Необходимо выполнить определенное планирование сетей, чтобы обеспечить подключение виртуальных машин Azure к сетям после отработки отказа и правильность их IP-адресов.

Перейдите к статье [Шаг 4. Планирование сетей для репликации физического сервера в Azure](hyper-v-site-walkthrough-network.md).

##  <a name="step-5-prepare-azure-resources"></a>Шаг 5. Подготовка ресурсов Azure

Прежде чем начать, настройте сети и хранилище Azure. Это можно сделать при развертывании, но мы рекомендуем это сделать до начала процесса.

Перейдите к статье [Шаг 5. Подготовка ресурсов Azure для репликации Hyper-V в Azure](hyper-v-site-walkthrough-prepare-azure.md).


## <a name="step-6-prepare-hyper-v"></a>Шаг 6. Подготовка Hyper-V

Убедитесь, что серверы Hyper-V соответствуют требованиям к развертыванию Site Recovery.

Перейдите к статье [Шаг 6. Подготовка узлов Hyper-V для репликации в Azure](hyper-v-site-walkthrough-prepare-hyper-v.md).

## <a name="step-7-set-up-a-vault"></a>Шаг 7. Настройка хранилища

Для координации процесса репликации и управления им необходимо настроить хранилище Recovery Services. При настройке хранилища укажите целевые объекты и место для репликации.

Перейдите к статье [Шаг 7. Настройка хранилища для репликации Hyper-V](hyper-v-site-walkthrough-create-vault.md).

## <a name="step-8-configure-source-and-target-settings"></a>Шаг 8. Настройка параметров исходной и целевой среды

Настройте исходную и целевую среды, которые используются для репликации. Настройка параметров исходной среды включает в себя добавление узлов Hyper-V на сайт Hyper-V, установку поставщика Site Recovery и агента служб восстановления на каждом узле Hyper-V и регистрацию сайта в хранилище служб восстановления.

Перейдите к разделу [Шаг 8. Настройка исходной и целевой среды для репликации физического сервера в Azure](hyper-v-site-walkthrough-source-target.md).

## <a name="step-9-set-up-a-replication-policy"></a>Шаг 9. Настройка политики репликации

Настройте политику, чтобы указать параметры репликации для виртуальных машин Hyper-V в хранилище.

Перейдите к разделу [Шаг 9. Настройка политики репликации для репликации физического сервера в Azure](hyper-v-site-walkthrough-replication.md).


## <a name="step-10-enable-replication"></a>Шаг 10. Включение репликации

После настройки и включения политики репликации выполняется начальная репликация виртуальной машины.

Перейдите к статье [Шаг 10. Включение репликации для физических серверов в Azure](hyper-v-site-walkthrough-enable-replication.md).

## <a name="step-11-run-a-test-failover"></a>Шаг 11. Запуск тестовой отработки отказа

После завершения начальной репликации и запуска репликации разностных изменений можно выполнить тестовую отработку отказа, чтобы убедиться, что все работает правильно.

Перейдите к статье [Шаг 11: Запуск тестовой отработки отказа физических серверов в Azure](hyper-v-site-walkthrough-test-failover.md).
