<properties
	pageTitle="Виртуальная машина Oracle WebLogic Server и Oracle Database | Microsoft Azure"
	description="Создайте образ Azure Oracle WebLogic Server 12c и Oracle Database 12c под управлением Windows Server 2012 с помощью модели развертывания диспетчера ресурсов."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rickstercdn" />

#Создание виртуальной машины Oracle WebLogic Server 12c и Oracle Database 12c в Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

В этой статье демонстрируется, как создать Oracle WebLogic Server 12c и базу данных Oracle Database 12c в образе, который ранее был создан с помощью программного обеспечения Oracle, установленного в Windows Server 2012 в Azure.

##Создание базы данных, размещенной на этой виртуальной машине

Следуйте указаниям в статье [Создание виртуальной машины Oracle Database 12c в Azure](virtual-machines-windows-classic-create-oracle-database.md), начиная с раздела **Создание базы данных с помощью виртуальной машины Oracle Database 12c в Azure**.

##Настройка Oracle WebLogic Server 12c, размещенного на этой виртуальной машине
Следуйте указаниям в разделе [Создание виртуальной машины Oracle WebLogic Server 12c в Azure](virtual-machines-windows-create-oracle-weblogic-server-12c.md), начиная с подраздела **Настройка виртуальной машины Oracle WebLogic Server 12c в Azure**.

##Дополнительные ресурсы
[Различные рекомендации по образам виртуальных машин Oracle](virtual-machines-windows-classic-oracle-considerations.md)

[Список образов виртуальных машин Oracle](virtual-machines-linux-classic-oracle-images.md)

[Подключение к базе данных Oracle из приложения Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Oracle WebLogic Server 12c с использованием Linux в Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0601_2016-->