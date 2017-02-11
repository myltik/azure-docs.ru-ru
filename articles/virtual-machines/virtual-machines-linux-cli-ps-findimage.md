---
title: "Выбор образов виртуальных машин Linux с помощью Azure CLI | Документация Майкрософт"
description: "Узнайте, как определить издателя, предложение и SKU для образов при создании виртуальной машины Linux с помощью развертывания посредством диспетчера ресурсов."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 732767bcece6d892aee7a8e6976f527fc66bb48d


---
# <a name="select-linux-vm-images-with-the-azure-cli"></a>Выбор образов виртуальных машин Linux с помощью интерфейса командной строки Azure (Azure CLI)
В этой статье описывается, как найти сведения об издателях, предложениях, номерах SKU и версиях для каждого расположения, в котором может выполняться развертывание. Возьмите за пример некоторые часто используемые образы виртуальных машин Linux:

**Таблица часто используемых образов Linux**

| PublisherName | ПРЕДЛОЖЕНИЕ | Sku |
|:--- |:--- |:--- |:--- |
| RedHat |RHEL |7,2 |
| credativ |Debian |8 |
| SUSE |openSUSE |13.2 |
| SUSE |SLES |12-SP1 |
| OpenLogic |CentOS |7.1. |
| Canonical |UbuntuServer |14.04.4-LTS |
| CoreOS |CoreOS |Stable |

[!INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]




<!--HONumber=Nov16_HO3-->


