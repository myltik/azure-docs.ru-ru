---
title: "Установка MongoDB на виртуальной машине Windows в Azure | Документация Майкрософт"
description: "Узнайте, как установить MongoDB на виртуальную машину Azure под управлением Windows Server, созданную с помощью классической модели развертывания."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6b5af18d02fd508a21cdc21b38b1c16e79f07ecb
ms.contentlocale: ru-ru
ms.lasthandoff: 06/26/2017


---
<a id="install-mongodb-on-a-windows-vm-in-azure" class="xliff"></a>

# Установите MongoDB на виртуальную машину Windows в Azure
> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../resource-manager-deployment-model.md).  В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Описание установки и настройки MongoDB с помощью модели развертывания Resource Manager см. в [этой статье](../install-mongodb.md).

[MongoDB][MongoDB] — это популярная высокопроизводительная база данных NoSQL с открытым кодом. В этой статье рассматривается создание виртуальной машины Windows Server с помощью [портала Azure][AzurePortal]. После этого перед установкой и настройкой MongoDB нужно создать и подключить к виртуальной машине диск данных. Если у вас есть виртуальная машина в Azure, которую вы хотите использовать, то можете перейти непосредственно к [установке и настройке MongoDB](#install-and-run-mongodb-on-the-virtual-machine).

<a id="create-a-virtual-machine-running-windows-server" class="xliff"></a>

## Создание виртуальной машины под управлением Windows Server
Чтобы создать виртуальную машину, выполните указанные ниже действия.

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> При создании виртуальной машины можно добавить конечную точку для MongoDB и настроить ее следующим образом: присвоить ей имя **Mongo**, в качестве протокола использовать **TCP**, а в качестве общедоступного и частного портов задать порт **27017**.
>
>

<a id="attach-a-data-disk" class="xliff"></a>

## Присоединение диска данных
Чтобы предоставить хранилище для виртуальной машины, подключите диск данных, а затем инициализируйте, чтобы его можно было использовать в Windows. Можно подключить существующий диск или пустой диск.

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

Указания по инициализации диска см. в разделе "Практическое руководство. Инициализация нового диска данных в Windows Server" в статье [Подключение диска данных к виртуальной машине Windows](attach-disk.md).

<a id="install-and-run-mongodb-on-the-virtual-machine" class="xliff"></a>

## Установка и запуск MongoDB на виртуальной машине
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

<a id="summary" class="xliff"></a>

## Сводка
В этом учебнике вы научились создавать виртуальную машину под управлением Windows Server, удаленно подключаться к ней и подключать диск данных.  Вы также узнали, как установить и настроить MongoDB на виртуальной машине под управлением Windows. Теперь вы можете получить доступ к MongoDB на виртуальной машине под управлением Windows, выполнив действия, описанные в дополнительных разделах [документации по MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/


