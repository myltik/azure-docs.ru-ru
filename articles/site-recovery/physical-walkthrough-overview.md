---
title: "Репликация локальных физических серверов в Azure с помощью службы Azure Site Recovery | Документация Майкрософт"
description: "В этой статье описаны действия, необходимые для репликации рабочих нагрузок, выполняющихся на локальных физических серверах Windows или Linux, в Azure с помощью службы Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017


---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Репликация физических серверов в Azure с помощью Site Recovery

В этой статье описаны действия для репликации локальных физических серверов Windows или Linux в Azure с помощью службы [Azure Site Recovery](site-recovery-overview.md) на портале Azure.


## <a name="step-1-review-architecture-and-prerequisites"></a>Шаг 1. Проверка архитектуры и предварительных требований

Перед началом развертывания ознакомьтесь с архитектурой сценария и убедитесь, что вам знакомы все компоненты, которые необходимы для настройки развертывания.

Перейдите к статье [Шаг 1. Обзор архитектуры репликации физического сервера в Azure](physical-walkthrough-architecture.md).


## <a name="step-2-review-prerequisites"></a>Шаг 2. Проверка предварительных требований

Убедитесь, что предварительные требования выполняются для каждого компонента развертывания.

- **Предварительные требования Azure**: учетная запись Microsoft Azure, сети Azure и учетные записи хранения.
- **Локальные компоненты Site Recovery**: компьютер, на котором запущены локальные компоненты Site Recovery.
- **Реплицируемые компьютеры**: реплицируемые серверы, соответствующие требованиям локальных компонентов и Azure.

Перейдите к статье [Шаг 2. Просмотр предварительных требований для репликации физического сервера в Azure](physical-walkthrough-prerequisites.md).

## <a name="step-3-plan-capacity"></a>Шаг 3. Планирование ресурсов

При выполнении полного развертывания необходимо выяснить, какие ресурсы требуются для репликации. Если выполняется быстрая настройка для тестирования среды, то этот шаг можно пропустить.

Перейдите к статье [Шаг 3. Планирование ресурсов и масштабирования для репликации физического сервера в Azure](physical-walkthrough-capacity.md).

## <a name="step-4-plan-networking"></a>Шаг 4. Планирование сетей

Необходимо выполнить определенное планирование сетей, чтобы обеспечить подключение виртуальных машин Azure к сетям после отработки отказа и правильность их IP-адресов.

Перейдите к статье [Шаг 4. Планирование сетей для репликации физического сервера в Azure](physical-walkthrough-network.md).

##  <a name="step-5-prepare-azure-resources"></a>Шаг 5. Подготовка ресурсов Azure

Прежде чем начать, настройте сети и хранилище Azure. 

Перейдите к статье [Шаг 5. Подготовка ресурсов Azure для репликации Hyper-V в Azure](physical-walkthrough-prepare-azure.md).


## <a name="step-6-set-up-a-vault"></a>Шаг 6. Настройка хранилища

Для координации процесса репликации и управления им необходимо настроить хранилище Recovery Services. При настройке хранилища укажите целевые объекты и место для репликации.

Перейдите к статье [Step 6: Set up a vault for physical server replication to Azure](physical-walkthrough-create-vault.md) (Шаг 6. Настройка хранилища для репликации физического сервера в Azure).

## <a name="step-7-configure-source-and-target-settings"></a>Шаг 7. Настройка параметров исходной и целевой среды

Настройте параметры исходного и целевого (Azure) сайта. Настройка параметров исходного сайта включает в себя выполнение единой установки для локальных компонентов Site Recovery.

Перейдите к статье [Шаг 7. Настройка исходной и целевой среды для репликации физического сервера в Azure](physical-walkthrough-source-target.md).

## <a name="step-8-set-up-a-replication-policy"></a>Шаг 8. Настройка политики репликации

Настройте политику, чтобы указать параметры репликации физических серверов.

Перейдите к разделу [Шаг 8. Настройка политики репликации для репликации физического сервера в Azure](physical-walkthrough-replication.md).

## <a name="step-9-install-the-mobility-service"></a>Шаг 9. Установка службы Mobility Service

Служба Mobility Service должна быть установлена на каждом сервере, который нужно реплицировать. Существует несколько способов настройки этой службы с помощью принудительной установки или установки по запросу.

Перейдите к разделу [Step 9: Install the Mobility service](physical-walkthrough-install-mobility.md) (Шаг 9. Установка службы Mobility Service).

## <a name="step-10-enable-replication"></a>Шаг 10. Включение репликации

После запуска службы Mobility Service на сервере можно включить для нее репликацию. После включения выполняется начальная репликация виртуальной машины.

Перейдите к статье [Шаг 10. Включение репликации для физических серверов в Azure](physical-walkthrough-enable-replication.md).

## <a name="step-11-run-a-test-failover"></a>Шаг 11. Запуск тестовой отработки отказа

После завершения начальной репликации и запуска репликации разностных изменений можно выполнить тестовую отработку отказа, чтобы убедиться, что все работает правильно.

Перейдите к статье [Шаг 11: Запуск тестовой отработки отказа физических серверов в Azure](physical-walkthrough-test-failover.md).


