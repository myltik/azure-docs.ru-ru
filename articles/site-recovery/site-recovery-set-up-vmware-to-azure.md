---
title: "Настройка целевой среды (из виртуальных машин VMware в Azure) | Документация Майкрософт"
description: "Сведения о настройке локальной среды для запуска репликации виртуальных машин VMware в Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 49cc01e54158b68e440f1efbfe2f8b0d076a5692
ms.contentlocale: ru-ru
ms.lasthandoff: 09/19/2017

---

# <a name="set-up-the-source-environment-vmware-to-azure"></a>Настройка целевой среды (из виртуальных машин VMware в Azure)
> [!div class="op_single_selector"]
> * [VMware в VMware](./site-recovery-set-up-vmware-to-azure.md)
> * [Из физического расположения в Azure](./site-recovery-set-up-physical-to-azure.md)

В этой статье приведены сведения о настройке локальной среды для запуска репликации виртуальных машин VMware в Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с руководством требуются следующие компоненты.
- Хранилище служб восстановления на [портале Azure](http://portal.azure.com "портал Azure").
- Специальная учетная запись на сервере VMware vCenter, которую можно использовать для [автоматического обнаружения](./site-recovery-vmware-to-azure.md).
- Виртуальная машина для установки сервера конфигурации.

## <a name="configuration-server-minimum-requirements"></a>Минимальные требования к серверу конфигурации
В следующей таблице перечислены минимальные требования к оборудованию, программному обеспечению и сети сервера конфигурации.

> [!IMPORTANT]
> При развертывании сервера конфигурации для защиты виртуальных машин VMware мы рекомендуем развернуть его как виртуальную машину с **высокой доступностью**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Сервер конфигурации не поддерживает прокси-серверы на основе HTTPS.

## <a name="choose-your-protection-goals"></a>Выбор целевых объектов для защиты

1. На портале Azure откройте колонку **Хранилища служб восстановления** и выберите свое хранилище.
2. В меню ресурсов хранилища выберите **Getting Started** > **Site Recovery** > **Step 1: Prepare Infrastructure** > **Protection goal** (Приступая к работе > Site Recovery > Шаг 1. Подготовка инфраструктуры > Цель защиты).

    ![Выбор цели](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. На странице **Protection goal** (Цель защиты) выберите **To Azure** (В Azure) и **Yes, with VMware vSphere Hypervisor** (Да, с использованием гипервизора VMware vSphere). Нажмите кнопку **ОК**.

    ![Выбор цели](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Настройка исходной среды
Настройка исходной среды состоит из двух основных задач.

- Установка и регистрация сервера конфигурации в службе Site Recovery.
- Обнаружение локальных виртуальных машин при подключении Azure Site Recovery к локальному серверу VMware vCenter или узлам vSphere EXSi.

### <a name="step-1-install-and-register-a-configuration-server"></a>Этап 1. Установка и регистрация сервера конфигурации

1. Щелкните **Шаг 1. Подготовка инфраструктуры** > **Источник**. Если у вас нет сервера конфигурации, в окне **Prepare source** (Подготовка источника) щелкните **+Configuration server** (+Сервер конфигурации).

    ![Настройка источника](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. В колонке **добавления сервера** в поле **Server type** (Тип сервера) должно быть значение **Configuration Server** (Сервер конфигурации).
4. Скачайте файл единой установки Site Recovery.
5. Скачайте ключ регистрации хранилища. При запуске программы единой установки вам потребуется ключ регистрации. Ключ действителен в течение пяти дней после создания.

    ![Настройка источника](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. На компьютере, используемом в качестве сервера конфигурации, запустите **программу единой установки Azure Site Recovery**, чтобы установить сервер конфигурации, сервер обработки и главный целевой сервер.

#### <a name="run-azure-site-recovery-unified-setup"></a>Выполнение единой установки Azure Site Recovery

> [!TIP]
> Если системное время на компьютере отличается от местного более чем на 5 минут, регистрация сервера конфигурации завершается сбоем. Перед началом установки синхронизируйте системное время с [сервером времени](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service).

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Сервер конфигурации можно установить с помощью командной строки. См. дополнительные сведения об [установке сервера конфигурации с помощью средств командной строки](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Добавление учетной записи VMware, используемой для автоматического обнаружения

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>Этап 2. Добавление сервера vCenter
Чтобы служба Azure Site Recovery могла обнаруживать виртуальные машины, запущенные в локальной среде, сервер VMware vCenter Server или узлы vSphere ESXi необходимо подключить к этой службе.

Нажмите кнопку **+vCenter** (+ vCenter Server), чтобы начать установку подключения сервера VMware vCenter Server или узла vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Распространенные проблемы
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Дальнейшие действия
[Настройка целевой среды](./site-recovery-prepare-target-vmware-to-azure.md) в Azure.

