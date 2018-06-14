---
title: " Управление сервером обработки, запущенным в Azure (модель Resource Manager) | Документация Майкрософт"
description: В этой статье описывается, как настроить сервер отработки для восстановления размещения (модель Resource Manager) в Azure.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767519"
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Управление сервером обработки, запущенным в Azure (модель Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Классическая модель](./site-recovery-vmware-setup-azure-ps-classic.md)

При наличии значительной задержки между виртуальной сетью Azure и локальной сетью при восстановлении размещения рекомендуется развертывать сервер обработки в Azure. В этой статье описывается, как выполнить установку, настройку и администрирование серверов обработки, работающих в Azure.

> [!NOTE]
> В статье описано, как использовать **Resource Manager** как модель развертывания для виртуальных машин при отработке отказа. Если вы используете **классическую** модель развертывания, выполните действия по [установке и настройке сервера обработки для восстановления размещения (классическая модель)](./site-recovery-vmware-setup-azure-ps-classic.md).

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Развертывание сервера обработки в Azure
1. В хранилище выберите **Инфраструктура Site Recovery** (под заголовком "Управление") > **Серверы конфигурации** (под заголовком "Для VMware и физических компьютеров") и укажите сервер конфигурации.
2. На открывшейся странице со сведениями о конфигурации сервера нажмите кнопку "+Сервер обработки".

  ![Добавление коллекции сервера обработки](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  На странице **Добавление сервера обработки** выберите следующие значения.

  ![Добавление элемента коллекции сервера обработки](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Имя поля**|**Значение**|
|-|-|
|Выберите расположение для развертывания сервера обработки.|Выберите вариант с **развертыванием сервера обработки для восстановления размещения в Azure**. |
|Подписка|Выберите подписку Azure для отработки отказа виртуальных машин|
|Группа ресурсов|Вы можете создать группу ресурсов для развертывания этого сервера обработки или развернуть сервер обработки в существующую группу ресурсов.|
|Расположение|Выберите центр обработки данных Azure для отработки отказа виртуальных машин.|
|Сеть Azure|Выберите виртуальную сеть Azure (VNet) для отработки отказа виртуальных машин. Если отработка отказа виртуальных машин выполняется в несколько виртуальных сетей Azure, сервер обработки нужно развернуть в каждую из них.|

4. Укажите остальные свойства для сервера обработки.

  ![Добавление сводных данных о сервере обработки](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Имя поля**|**Значение**|
|-|-|
|Имя сервера|Отображаемое имя и имя узла для виртуальной машины на сервере обработки.|
| Имя пользователя|Имя пользователя, который становится администратором на этой виртуальной машине.|
|Учетная запись хранения|Имя учетной записи хранения, где размещен виртуальный диск виртуальной машины.|
|Подсеть|Подсеть виртуальной сети Azure, к которой подключена виртуальная машина.|
| IP-адрес|IP-адрес, используемый сервером обработки после загрузки.|
5. Нажмите кнопку "ОК", чтобы начать развертывание виртуальной машины на сервере обработки.

> [!NOTE]
> Чтобы использовать этот сервер обработки для восстановления размещения, необходимо зарегистрировать его на локальном сервере конфигурации.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Регистрация сервера обработки (запущенного в Azure) на сервере конфигурации (запущенного локально)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Обновление сервера обработки до последней версии

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Отмена регистрации сервера обработки (запущенного в Azure) на сервере конфигурации (запущенного локально)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
