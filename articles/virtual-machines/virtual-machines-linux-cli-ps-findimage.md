<properties
   pageTitle="Просмотр и выбор образов виртуальных машин Linux | Microsoft Azure"
   description="Узнайте, как определить издателя, предложение и SKU для образов при создании виртуальной машины Linux с помощью развертывания посредством диспетчера ресурсов."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="03/14/2016"
   ms.author="rasquill"/>

# Просмотр и выбор образов виртуальных машин Linux в Azure с помощью интерфейса командной строки или оболочки PowerShell

В этой статье описывается, как найти сведения об издателях, предложениях, номерах SKU и версиях для каждого расположения, в котором может выполняться развертывание. Возьмите за пример некоторые часто используемые образы виртуальных машин Linux:

**Таблица часто используемых образов Linux**


| PublisherName | ПРЕДЛОЖЕНИЕ | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1. |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0330_2016-->