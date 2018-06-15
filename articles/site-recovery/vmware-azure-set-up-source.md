---
title: Настройка исходного окружения для репликации из VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье описано, как настроить локальное окружение для репликации виртуальных машин VMware в Azure с помощью Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: b2c564e8d49e39d9cdc09d3fe168388d579de70e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
ms.locfileid: "29811091"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Настройка исходного окружения для репликации из VMware в Azure

В этой статье описано, как настроить исходное локальное окружение для репликации виртуальных машин VMware в Azure. В ней указаны шаги для выбора сценария репликации, настройки локального компьютера в качестве сервера конфигурации Azure Site Recovery и автоматического обнаружения локальных виртуальных машин. 

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается, что вы:
- [настроили ресурсы](tutorial-prepare-azure.md) на [портале Azure](http://portal.azure.com);
- [настроили локальную среду VMware](vmware-azure-tutorial-prepare-on-premises.md), в том числе выделенную учетную запись для автоматического обнаружения.



## <a name="choose-your-protection-goals"></a>Выбор целевых объектов для защиты

1. На портале Azure откройте колонку **Хранилища служб восстановления** и выберите свое хранилище.
2. В меню ресурсов хранилища выберите **Getting Started** > **Site Recovery** > **Step 1: Prepare Infrastructure** > **Protection goal** (Приступая к работе > Site Recovery > Шаг 1. Подготовка инфраструктуры > Цель защиты).

    ![Выбор цели](./media/vmware-azure-set-up-source/choose-goals.png)
3. На странице **Protection goal** (Цель защиты) выберите **To Azure** (В Azure) и **Yes, with VMware vSphere Hypervisor** (Да, с использованием гипервизора VMware vSphere). Нажмите кнопку **ОК**.

    ![Выбор цели](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Настройка сервера конфигурации

Настройте сервер конфигурации в качестве локальной виртуальной машины VMware и используйте шаблон Open Virtualization Format (OVF). Ознакомьтесь с [дополнительными сведениями](concepts-vmware-to-azure-architecture.md) о компонентах, которые будут установлены на виртуальной машине VMware. 

1. Изучите дополнительные сведения о [необходимых компонентах](vmware-azure-deploy-configuration-server.md#prerequisites) для развертывания сервера конфигурации.
2. [Проверьте значения емкости](vmware-azure-deploy-configuration-server.md#capacity-planning) для развертывания.
3. [Загрузите](vmware-azure-deploy-configuration-server.md#download-the-template) и [импортируйте](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) шаблон OVF (how-to-deploy-configuration-server.md) для настройки локальной виртуальной машины VMware, работающей на сервере конфигурации.
4. Включите виртуальную машину VMware и [зарегистрируйте ее](vmware-azure-deploy-configuration-server.md#register-the-configuration-server) в хранилище служб восстановления.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Добавление учетной записи VMware, используемой для автоматического обнаружения

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Подключение к серверу VMware

Чтобы служба Azure Site Recovery могла обнаруживать виртуальные машины, запущенные в локальной среде, сервер VMware vCenter Server или узлы vSphere ESXi необходимо подключить к этой службе.

Нажмите кнопку **+vCenter** (+ vCenter Server), чтобы начать установку подключения сервера VMware vCenter Server или узла vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Распространенные проблемы
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Дополнительная информация
[Настройка целевой среды](./vmware-azure-set-up-target.md) в Azure.
