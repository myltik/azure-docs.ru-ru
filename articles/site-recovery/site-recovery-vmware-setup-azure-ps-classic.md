---
title: " Управление сервером обработки, запущенным в Azure (классическая модель) | Документация Майкрософт"
description: "В этой статье описывается, как настроить сервер отработки для восстановления размещения (классическая модель) в Azure."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 479bbd207bcf715138c340f9e4d2634120bab85c
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017

---

# <a name="manage-a-process-server-running-in-azure-classic"></a>Управление сервером обработки, запущенным в Azure (классическая модель)
> [!div class="op_single_selector"]
> * [Классическая модель Azure](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [Диспетчер ресурсов](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

При наличии значительной задержки между виртуальной сетью Azure и локальной сетью при восстановлении размещения рекомендуется развертывать сервер обработки в Azure. В этой статье описывается, как выполнить установку, настройку и администрирование серверов обработки, работающих в Azure.

> [!NOTE]
> Эта статья предназначена для тех, кто использовал классическую модель развертывания для виртуальных машин при отработке отказа. Если вы использовали модель развертывания с помощью Resource Manager, выполните инструкции в разделе [Управление сервером обработки, запущенным в Azure (модель Resource Manager)](./site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Развертывание сервера обработки в Azure

1. В Azure Marketplace создайте виртуальную машину с помощью **Microsoft Azure Site Recovery Process Server V2**. </br>
    ![](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)Marketplace_image_1
2. Выберите **классическую** модель развертывания. </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. В мастере создания виртуальной машины выберите "Основные параметры" и укажите подписку и расположение для отработки отказа виртуальных машин.</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. Убедитесь, что виртуальная машина подключена к виртуальной сети Azure, к которой подключена виртуальная машина для отработки отказа.</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. Подготовив виртуальную машину на сервере обработки, войдите и зарегистрируйтесь на сервере конфигурации.

> [!NOTE]
> Чтобы использовать этот сервер обработки для восстановления размещения, необходимо зарегистрировать его на локальном сервере конфигурации.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Регистрация сервера обработки (запущенного в Azure) на сервере конфигурации (запущенного локально)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Обновление сервера обработки до последней версии

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Отмена регистрации сервера обработки (запущенного в Azure) на сервере конфигурации (запущенного локально)

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

