<properties
   pageTitle="Поиск и выбор образов виртуальных машин Windows | Microsoft Azure"
   description="Узнайте, как определить издателя, предложение и номер SKU для образов при создании виртуальной машины Windows с помощью развертывания посредством диспетчера ресурсов."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки

В этой статье описывается, как найти сведения об издателях, предложениях, номерах SKU и версиях для каждого расположения, в котором может выполняться развертывание. Для примера вот некоторые часто используемые образы виртуальных машин Windows:

## Таблица часто используемых образов Windows


| PublisherName | ПРЕДЛОЖЕНИЕ | Sku |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| CoreOS | CoreOS | Beta |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | SQL2014 WS2012R2 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | SQL2014 WS2012R2 | Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<!---HONumber=AcomDC_0601_2016-->