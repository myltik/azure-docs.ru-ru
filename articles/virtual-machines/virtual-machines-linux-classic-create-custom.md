---
title: "Создание классической виртуальной машины Linux с помощью интерфейса командной строки | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину Linux с помощью интерфейса командной строки Azure, используя классическую модель развертывания."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: d3817e5e0f2c237375cb36993661968358c28b55


---
# <a name="how-to-create-a-linux-vm-with-the-azure-cli"></a>Создание виртуальной машины Linux с помощью интерфейса командной строки Azure
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Версия для модели развертывания с помощью Resource Manager доступна [здесь](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В этой статье описано создание виртуальной машины Linux с помощью интерфейса командной строки Azure и классической модели развертывания. Мы используем образ Linux из раздела **ОБРАЗЫ** в Azure. Интерфейс командной строки Azure позволяет настраивать, помимо прочих, следующие параметры:

* Подключение виртуальной машины к виртуальной сети.
* Добавление виртуальной машины к существующей облачной службе.
* Добавление виртуальной машины к существующей учетной записи хранения
* Добавление виртуальной машины в группу доступности или расположение.

> [!IMPORTANT]
> Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительную информацию о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Создание виртуальной машины Linux с использованием классической модели развертывания
[!INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]




<!--HONumber=Dec16_HO1-->


