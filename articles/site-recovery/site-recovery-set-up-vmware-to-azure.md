---
title: Настройка целевой среды (из виртуальных машин VMware в Azure) | Документация Майкрософт
description: Сведения о настройке локальной среды для запуска репликации виртуальных машин VMware в Azure.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767579"
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Настройка целевой среды (из виртуальных машин VMware в Azure)
> [!div class="op_single_selector"]
> * [VMware в VMware](./site-recovery-set-up-vmware-to-azure.md)
> * [Из физического расположения в Azure](./site-recovery-set-up-physical-to-azure.md)

В этой статье приведены сведения о настройке локальной среды для репликации виртуальных машин VMware в Azure. В ней указаны шаги для выбора сценария репликации, настройки локального компьютера в качестве сервера конфигурации Azure Site Recovery и автоматического обнаружения локальных виртуальных машин. 

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается, что вы:
- [настроили ресурсы](tutorial-prepare-azure.md) на [портале Azure](http://portal.azure.com);
- [настроили локальную среду VMware](tutorial-prepare-on-premises-vmware.md), в том числе выделенную учетную запись для автоматического обнаружения.



## <a name="choose-your-protection-goals"></a>Выбор целевых объектов для защиты

1. На портале Azure откройте колонку **Хранилища служб восстановления** и выберите свое хранилище.
2. В меню ресурсов хранилища выберите **Getting Started** > **Site Recovery** > **Step 1: Prepare Infrastructure** > **Protection goal** (Приступая к работе > Site Recovery > Шаг 1. Подготовка инфраструктуры > Цель защиты).

    ![Выбор цели](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. На странице **Protection goal** (Цель защиты) выберите **To Azure** (В Azure) и **Yes, with VMware vSphere Hypervisor** (Да, с использованием гипервизора VMware vSphere). Нажмите кнопку **ОК**.

    ![Выбор цели](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Настройка сервера конфигурации

Настройте сервер конфигурации в качестве локальной виртуальной машины VMware и используйте шаблон Open Virtualization Format (OVF). Ознакомьтесь с [дополнительными сведениями](concepts-vmware-to-azure-architecture.md) о компонентах, которые будут установлены на виртуальной машине VMware. 

1. Изучите дополнительные сведения о [необходимых компонентах](how-to-deploy-configuration-server.md#prerequisites) для развертывания сервера конфигурации. [Проверьте значения емкости](how-to-deploy-configuration-server.md#capacity-planning) для развертывания.
2. [Загрузите](how-to-deploy-configuration-server.md#download-the-template) и [импортируйте](how-to-deploy-configuration-server.md#import-the-template-in-vmware) шаблон OVF (how-to-deploy-configuration-server.md) для настройки локальной виртуальной машины VMware, работающей на сервере конфигурации.
3. Включите виртуальную машину VMware и [зарегистрируйте ее](how-to-deploy-configuration-server.md#register-the-configuration-server) в хранилище служб восстановления.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Добавление учетной записи VMware, используемой для автоматического обнаружения

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Подключение к серверу VMware

Чтобы служба Azure Site Recovery могла обнаруживать виртуальные машины, запущенные в локальной среде, сервер VMware vCenter Server или узлы vSphere ESXi необходимо подключить к этой службе.

Нажмите кнопку **+vCenter** (+ vCenter Server), чтобы начать установку подключения сервера VMware vCenter Server или узла vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Распространенные проблемы
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Дополнительная информация
[Настройка целевой среды](./site-recovery-prepare-target-vmware-to-azure.md) в Azure.
