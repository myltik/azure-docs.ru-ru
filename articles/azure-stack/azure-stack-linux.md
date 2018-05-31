---
title: Добавление образов Linux в Azure Stack
description: Сведения о том, как добавлять образы Linux в Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935723"
---
# <a name="add-linux-images-to-azure-stack"></a>Добавление образов Linux в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Виртуальные машины Linux можно развернуть в Azure Stack, добавив образ на базе Linux в Azure Stack Marketplace. Проще всего добавить образ Linux в Azure Stack с помощью управления Marketplace. Эти образы были подготовлены и протестированы на совместимость с Azure Stack.

## <a name="marketplace-management"></a>Управление Marketplace

Для загрузки образов Linux из Azure Marketplace используйте процедуры, приведенные в следующей статье. Выберите образы Linux, которые вы хотите предложить пользователям в Azure Stack. 

[Скачайте элементы Marketplace из Azure в Azure Stack](azure-stack-download-azure-marketplace-item.md).

Обратите внимание, что эти образы часто обновляются, поэтому чаще посещайте портал управления marketplace, чтобы обеспечить их актуальное состояние.

## <a name="prepare-your-own-image"></a>Подготовка собственного образа

 По возможности скачивайте образы, доступные на портале управления marketplace. Они будут подготовлены и протестированы для Azure Stack. 
 
 Необходим агент Linux для Azure (обычно называется `WALinuxAgent` или `walinuxagent`), и не все версии агента будут работать с Azure Stack. Следует использовать версию 2.2.18 или более позднюю версию, если вы создаете собственный образ. Обратите внимание на то, что в настоящее время [cloud-init](https://cloud-init.io/) не поддерживается в Azure Stack.

 Можно подготовить свой собственный образ Linux с помощью следующих инструкций.

   * [Подготовка виртуальной машины на основе CentOS для Azure](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Подготовка виртуального жесткого диска Debian для Azure](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Подготовка виртуальной машины на основе Red Hat для Azure](azure-stack-redhat-create-upload-vhd.md)
   * [Подготовка виртуальной машины SLES или openSUSE для Azure](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Сервер Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Добавление образа в marketplace
 
Следуйте указаниям по [добавлению образа в marketplace](azure-stack-add-vm-image.md). Убедитесь, что для параметра `OSType` задано значение `Linux`.

После добавления образа в Marketplace создается элемент Marketplace, и пользователи могут развернуть виртуальную машину Linux.
