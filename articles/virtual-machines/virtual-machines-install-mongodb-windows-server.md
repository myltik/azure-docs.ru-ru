<properties
	pageTitle="Установка MongoDB на виртуальной машине под управлением Windows Server"
	description="Узнайте, как установить MongoDB на виртуальной машине Azure под управлением Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="dkshir"/>

#Установка MongoDB на виртуальной машине под управлением Windows Server в Azure

[MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом. С помощью [портала управления Azure][AzureManagementPortal] можно создать виртуальную машину под управлением Windows Server из коллекции образов. Затем можно установить и настроить базу данных MongoDB на виртуальной машине.


## Создание виртуальной машины под управлением Windows Server

Чтобы создать виртуальную машину, выполните указанные ниже действия.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

> [AZURE.NOTE]При создании виртуальной машины можно добавить конечную точку для MongoDB и настроить ее следующим образом: присвоить ей имя **Mongo**, в качестве протокола использовать **TCP**, а в качестве общедоступного и частного портов задать порт **27017**.

## Присоединение диска данных
Чтобы предоставить хранилище для виртуальной машины, подключите диск данных, а затем инициализируйте, чтобы его можно было использовать в Windows. Можно подключать как существующий диск, если имеются данные, которые планируется использовать, так и пустой диск.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Инструкции о том, как инициализировать диск, см. в разделе "Инструкции по инициализации нового диска с данными в Windows Server" в статье [Подключение диска с данными к виртуальной машине Windows](storage-windows-attach-disk.md).

## Установка и запуск MongoDB на виртуальной машине

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Сводка
В этом учебнике вы научились создавать виртуальную машину Windows Server, удаленно подключаться к ней и подключать диск данных. Вы также узнали, как установить и настроить MongoDB на виртуальной машине Windows. Теперь вы можете получить доступ к MongoDB на виртуальной машине Windows, выполнив действия, описанные в дополнительных разделах [документации MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!---HONumber=July15_HO4-->