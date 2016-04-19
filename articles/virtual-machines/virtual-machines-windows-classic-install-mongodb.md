<properties
	pageTitle="Установка MongoDB на виртуальную машину Windows | Microsoft Azure"
	description="Узнайте, как установить MongoDB на виртуальную машину Azure под управлением Windows Server, созданную с помощью классической модели развертывания."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="iainfou"/>

#Установка MongoDB на виртуальную машину Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


[MongoDB][MongoDB] является популярной высокопроизводительной базой данных NoSQL с открытым кодом. С помощью [классического портала Azure][AzurePortal] можно создать виртуальную машину под управлением Windows Server из коллекции образов с помощью классической модели развертывания. Затем можно установить и настроить базу данных MongoDB на виртуальной машине.


## Создание виртуальной машины под управлением Windows Server

Чтобы создать виртуальную машину, выполните указанные ниже действия.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] При создании виртуальной машины можно добавить конечную точку для MongoDB и настроить ее следующим образом: присвоить ей имя **Mongo**, в качестве протокола использовать **TCP**, а в качестве общедоступного и частного портов задать порт **27017**.

## Присоединение диска данных
Чтобы предоставить хранилище для виртуальной машины, подключите диск данных, а затем инициализируйте, чтобы его можно было использовать в Windows. Можно подключать как существующий диск, если имеются данные, которые планируется использовать, так и пустой диск.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Указания по инициализации диска см. в разделе «Практическое руководство. Инициализация нового диска данных в Windows Server» в статье [Подключение диска данных к виртуальной машине Windows](virtual-machines-windows-classic-attach-disk.md).

## Установка и запуск MongoDB на виртуальной машине

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Сводка
В этом учебнике вы научились создавать виртуальную машину под управлением Windows Server, удаленно подключаться к ней и подключать диск данных. Вы также узнали, как установить и настроить MongoDB на виртуальной машине под управлением Windows. Теперь вы можете получить доступ к MongoDB на виртуальной машине под управлением Windows, выполнив действия, описанные в дополнительных разделах [документации по MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0406_2016-->