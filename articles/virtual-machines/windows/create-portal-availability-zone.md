---
title: Создание зональной виртуальной машины Windows с помощью портала Azure | Документация Майкрософт
description: Создание виртуальной машины Windows в зоне доступности с помощью портала Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3d3561cf1ad760930821fabeef9839c25d55f2a9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30321994"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Создание виртуальной машины Windows в зоне доступности с помощью портала Azure

В этой статье рассматривается использование портала Azure для создания виртуальной машины в зоне доступности Azure. [Зона доступности](../../availability-zones/az-overview.md) представляет собой физически отдельную зону в регионе Azure. Зоны доступности позволяют защитить приложения и данные от маловероятных сбоев и потери всего центра обработки данных.

Чтобы использовать зону доступности, создайте виртуальную машину в [поддерживаемом регионе Azure](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

## <a name="log-in-to-azure"></a>Вход в Azure 

Войдите на портал Azure по адресу https://portal.azure.com.

## <a name="create-virtual-machine"></a>Создание виртуальной машины

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.

2. Выберите **Вычисления**, а затем — **Windows Server 2016 Datacenter**. 

3. Введите сведения о виртуальной машине. Имя пользователя и пароль понадобятся для входа на виртуальную машину. Пароль должен включать минимум 12 символов и соответствовать [определенным требованиям к сложности](faq.md#what-are-the-password-requirements-when-creating-a-vm). Выберите расположение, например "Восточная часть США 2", поддерживающее зоны доступности. По завершении нажмите кнопку **ОК**.

    ![Ввод основных сведений о виртуальной машине в колонке портала](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Выберите размер виртуальной машины. Выберите рекомендуемый размер или используйте фильтр на основе функций. Убедитесь, что выбранный размер доступен в зоне, которую вы хотите использовать.

    ![Выбор размера виртуальной машины](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. В разделе **Параметры** > **Высокий уровень доступности** выберите одну из зон из раскрывающегося списка **зон доступности**. Оставьте значения по умолчанию, а затем нажмите кнопку **ОК**.

    ![Выбор зоны доступности](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. На странице "Сводка" нажмите кнопку **Создать**, чтобы начать развертывание виртуальной машины.

7. Виртуальная машина будет закреплена на панели мониторинга портала Azure. Когда развертывание завершится, автоматически отобразятся сводные сведения о виртуальной машине.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Подтверждение зоны для управляемого диска и IP-адреса

При развертывании виртуальной машины в зоне доступности диск виртуальной машины создается в той же зоне доступности. По умолчанию общедоступный IP-адрес также создается в этой зоне.

Параметры зоны для этих ресурсов можно подтвердить на портале.  

1. Щелкните **Группы ресурсов** и выберите имя группы ресурсов для виртуальной машины, например *myResourceGroup*.

2. Щелкните имя ресурса диска. Страница **Обзор** содержит подробные сведения о расположении и зоне доступности ресурса.

    ![Зона доступности для управляемого диска](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Щелкните имя ресурса общедоступного IP-адреса. Страница **Обзор** содержит подробные сведения о расположении и зоне доступности ресурса.

    ![Зона доступности для общедоступного IP-адреса](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Дополнительная информация

Из этой статье вы узнали, как создать виртуальную машину в зоне доступности. Узнайте о [регионах и доступности](regions-and-availability.md) виртуальных машин Azure.
