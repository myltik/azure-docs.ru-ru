---
title: Создание классической виртуальной машины Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с помощью Azure CLI 1.0, используя классическую модель развертывания.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 6d3f0dd0c82ad32df4d6e17058d9b1bea57c301f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Как создать виртуальную машину Linux с помощью Azure CLI 1.0
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Версия для модели развертывания с помощью Resource Manager доступна [здесь](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В этой статье описано создание виртуальной машины Linux с помощью Azure CLI 1.0 и классической модели развертывания. Мы используем образ Linux из раздела **ОБРАЗЫ** в Azure. Команды Azure CLI 1.0 позволяют настраивать, помимо прочих, следующие параметры.

* Подключение виртуальной машины к виртуальной сети.
* Добавление виртуальной машины к существующей облачной службе.
* Добавление виртуальной машины к существующей учетной записи хранения
* Добавление виртуальной машины в группу доступности или расположение.

> [!IMPORTANT]
> Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительную информацию о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Создание виртуальной машины Linux с использованием классической модели развертывания
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

