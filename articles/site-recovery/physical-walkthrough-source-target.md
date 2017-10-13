---
title: "Настройка исходной и целевой среды для репликации физического сервера в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье кратко описаны шаги для настройки параметров исходной и целевой среды при репликации физических серверов в службу хранилища Azure с помощью службы Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>Шаг 7. Настройка исходной и целевой среды для репликации физического сервера в Azure

В этой статье описано, как настроить параметры исходной и целевой среды при репликации локальных физических серверов в Azure с помощью службы [Azure Site Recovery](site-recovery-overview.md) на портале Azure.

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Настройка исходной среды

Настройте сервер конфигурации, зарегистрируйте его в хранилище и найдите виртуальные машины.

1. Щелкните **Site Recovery** > **Шаг 1. Подготовка инфраструктуры** > **Источник**.
2. Если у вас нет сервера конфигурации, щелкните **+Configuration server** (+ Сервер конфигурации).
3. В колонке **Добавление сервера** в поле **Тип сервера** должно быть указано **Сервер конфигурации**.
4. Скачайте файл единой установки Site Recovery.
5. Скачайте ключ регистрации хранилища. Он потребуется при запуске единой установки. Ключ действителен в течение пяти дней после создания.

   ![Настройка источника](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Регистрация сервера конфигурации в хранилище

Прежде чем начать, выполните приведенные ниже действия, после чего запустите программу единой установки, чтобы установить сервер конфигурации, сервер обработки и главный целевой сервер. Просмотрите видео, в котором описано настройку компонентов для репликации виртуальных машин VMware в Azure. Но этот процесс можно также применить при репликации физического сервера в Azure.

- Убедитесь, что на виртуальной машине сервера конфигурации системные часы синхронизированы с [сервером времени](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Значения времени должны совпадать. Если системные часы отстают или спешат в пределах 15 минут, установка может завершиться ошибкой.
- На компьютере сервера конфигурации запустите программу установки от имени локального администратора.
- Включите на компьютере протокол TLS 1.0.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Сервер конфигурации можно также установить [с помощью командной строки](http://aka.ms/installconfigsrv).




## <a name="set-up-the-target-environment"></a>Настройка целевой среды

Перед настройкой целевой среды убедитесь, что учетная запись хранения Azure и виртуальная сеть настроены.

1. Щелкните **Подготовка инфраструктуры** > **Цель** и выберите требуемую подписку Azure.
2. Укажите, какая целевая модель развертывания используется: классическая или Resource Manager.
3. Site Recovery проверяет наличие одной или нескольких совместимых учетных записей хранения и сетей Azure.

   ![Цель](./media/physical-walkthrough-source-target/gs-target.png)

4. Если вы еще не создали учетную запись хранения или сеть, щелкните **+Учетная запись хранения** или **+Сеть**, чтобы создать учетную запись Resource Manager или сеть.

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к разделу [Шаг 8. Настройка политики репликации для репликации физического сервера в Azure](physical-walkthrough-replication.md).
