<properties
	pageTitle="Создание виртуальной машины под управлением Windows в Azure"
	description="Информация о создании виртуальной машины под управлением Windows на портале Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="kathydav"/>

# Создание виртуальной машины под управлением Windows на портале Azure

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)


В этом учебнике показано, насколько просто на портале Azure можно создать виртуальную машину Azure. Мы будем использовать в качестве примера образ Windows Server, но это лишь один из многих образов, предлагаемых в Azure. Обратите внимание, что доступные образы зависят от подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов.

Можно также создавать виртуальные машины с помощью [собственных образов](virtual-machines-create-upload-vhd-windows-server.md). Дополнительную информацию об этом и других методах см. в статье [Различные способы создания виртуальной машины Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Видеоруководство

Ниже приведено видео с пошаговым выполнением действий по этому учебнику.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Как создать виртуальную машину

В этом разделе рассматривается использование метода **Из коллекции** на портале Azure для создания виртуальной машины. Указанный вариант предлагает больший выбор настроек, чем метод **Быстрое создание**. Например, при необходимости присоединить виртуальную машину к виртуальной сети следует использовать параметр **Из коллекции**.

> [AZURE.NOTE]На [портале предварительной версии Azure](https://portal.azure.com) тоже можно создавать виртуальные машины, применять расширенные функции мониторинга и диагностики виртуальных машин, использовать хранилище класса Premium и многое другое. Этот портал имеет больше функциональных возможностей и поддается дополнительной настройке. Доступные параметры конфигурации виртуальных машин на двух порталах в значительной степени одинаковы, но не полностью. Например, на портале предварительной версии можно настроить виртуальную машину с хранилищем класса Premium.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Дальнейшие действия

- Войдите в виртуальную машину. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server](virtual-machines-log-on-windows-server.md).

- Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Указания см. в статье [Учебник по подключению диска данных](storage-windows-attach-disk.md).

## Дополнительные ресурсы

Дополнительные сведения о том, что можно настроить для виртуальной машины и когда это можно сделать, см. в статье [О параметрах конфигурации ВМ Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!---HONumber=August15_HO8-->