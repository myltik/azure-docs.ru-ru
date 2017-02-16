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
ms.workload: backup-recovery
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 9dfcdeb4dc3e84089eb8031272b870f87d90b689
ms.openlocfilehash: 2256ba395e8d5f7a1ce7d4d78168a9cc51d4f074

---

# <a name="set-up-the-source-environment-vmware-to-azure"></a>Настройка целевой среды (из виртуальных машин VMware в Azure)
> [!div class="op_single_selector"]
> * [Виртуальные машины VMware](./site-recovery-set-up-vmware-to-azure.md)
> * [Физические серверы](./site-recovery-set-up-physical-to-azure.md)

В этой статье приведены сведения о настройке локальной среды для запуска репликации виртуальных машин VMware в Azure.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас уже имеется:
1. Хранилище служб восстановления на [портале Azure](http://portal.azure.com "портал Azure").
2. Специальная учетная запись на сервере VMware vCenter, которую можно использовать для [автоматического обнаружения](./site-recovery-vmware-to-azure.md#vmware-account-permissions).
3. Виртуальная машина для установки сервера конфигурации.

### <a name="configuration-server-minimum-requirements"></a>Минимальные требования к серверу конфигурации
Программное обеспечение сервера конфигурации необходимо развернуть на **высокодоступной** виртуальной машине VMware. В следующей таблице перечислены минимальные требования к оборудованию, программному обеспечению и сети сервера конфигурации.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-server-requirements.md)]

> [!NOTE]
> Сервер конфигурации не поддерживает прокси-серверы на основе HTTPS.

## <a name="choose-your-protection-goals"></a>Выбор целевых объектов для защиты

1. На портале Azure откройте колонку **Хранилища служб восстановления** и выберите свое хранилище.
2. В меню ресурсов хранилища выберите **Приступая к работе** > **Site Recovery** > **Шаг 1. Подготовка инфраструктуры** > **Цель защиты**.

    ![Выбор цели](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. На странице **Цель защиты** выберите **В Azure** и **Да, с использованием низкоуровневой оболочки VMware vSphere**. Нажмите кнопку **ОК**.

    ![Выбор цели](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Настройка исходной среды
Настройка исходной среды состоит из двух основных задач:

1. Установка и регистрация сервера конфигурации в службе Site Recovery.
2. Обнаружение локальных виртуальных машин за счет подключения Azure Site Recovery к локальному серверу VMware vCenter или узлам vSphere EXSi.

### <a name="step-1-install--register-a-configuration-server"></a>Этап 1. Установка и регистрация сервера конфигурации

1. Щелкните **Шаг 1. Подготовка инфраструктуры** > **Источник**. Если у вас нет сервера конфигурации, в окне **Подготовка источника** щелкните **Добавить сервер конфигурации**.

    ![Настройка источника](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. В колонке **Добавление сервера** в поле **Тип сервера** должно быть указано **Сервер конфигурации**.
4. Скачайте файл единой установки Site Recovery.
5. Скачайте ключ регистрации хранилища. При запуске программы единой установки вам потребуется ключ регистрации. Этот ключ действителен в течение **пяти** дней после создания.

    ![Настройка источника](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. На компьютере, используемом в качестве сервера конфигурации, запустите **программу единой установки Azure Site Recovery**, чтобы установить сервер конфигурации, сервер обработки и главный целевой сервер.

#### <a name="running-the-azure-site-recovery-unified-setup"></a>Выполнение единой установки Azure Site Recovery

> [!TIP]
> Если системное время на компьютере не совпадает с местным временем более чем на&5; минут, регистрация сервера конфигурации завершается сбоем. Перед началом установки синхронизируйте системное время с [сервером времени](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service).

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Сервер конфигурации можно установить с помощью командной строки. Дополнительные сведения об установке сервера конфигурации с помощью программы командной строки см. [здесь](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Добавление учетной записи VMware, используемой для автоматического обнаружения

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>Этап 2. Добавление сервера vCenter
Чтобы служба Azure Site Recovery могла обнаруживать виртуальные машины, запущенные в локальной среде, сервер VMware vCenter Server или узлы vSphere ESXi необходимо подключить к этой службе.

Нажмите кнопку +vCenter (Добавить vCenter Server), чтобы запустить подключение сервера VMware vCenter Server или узла vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Распространенные проблемы
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Дальнейшие действия
Следующий этап заключается в [настройке целевой среды](./site-recovery-vmware-to-azure.md#step-3-set-up-the-target-environment) в Azure.



<!--HONumber=Jan17_HO2-->


