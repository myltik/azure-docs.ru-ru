---
title: Устранение неполадок дисков, подключенных к виртуальным машинам Azure | Документация Майкрософт
description: Хранилище BLOB-объектов предназначено для хранения больших объемов неструктурированных данных объектов, например текстовых или двоичных данных. Ваши приложения могут обращаться к объектам в хранилище BLOB-объектов из PowerShell или Azure CLI, а также из кода с помощью клиентских библиотек хранилища Azure или через REST.
services: storage
author: genlin
manager: cshepard
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 766062b085c359499046151f337921a51d948715
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362712"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Устранение неполадок дисков, подключенных к виртуальным машинам Azure 

В виртуальных машинах Azure для диска операционной системы и всех подключенных дисков данных используются виртуальные жесткие диски (VHD). VHD хранятся в одной или нескольких учетных записях хранения Azure как страничные BLOB-объекты. В этой статье приведены указания по устранению распространенных неполадок с VHD. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Устранение неполадок с удалением ресурсов хранилища виртуальной машины

В некоторых случаях может возникнуть ошибка при удалении ресурса хранилища, если виртуальная машина в развертывании Resource Manager содержит подключенные VHD. Помощь в устранении этой проблемы можно получить, ознакомившись с одной из следующих статей: 

  * Для виртуальных машин Linux: [Ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md).  
  * Для виртуальных машин Windows: [Ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md).  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Устранение непредвиденных перезагрузок виртуальных машин с подключенными VHD

Если происходят непредвиденные перезагрузки виртуальной машины с большим числом подключенных VHD, ознакомьтесь со следующими статьями:

  * Для виртуальных машин Linux: [Непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md).
  * Для виртуальных машин Windows: [Непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md).
