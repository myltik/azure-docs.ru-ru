<properties
	pageTitle="Создание виртуальной машины под управлением Windows в Azure"
	description="Узнайте, как создать виртуальную машину Windows на портале управления Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# Создание виртуальной машины под управлением Windows на портале управления Azure

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)


В этом учебнике показано, насколько просто на портале управления Azure можно создать виртуальную машину Azure. Мы будем использовать в качестве примера образ Windows Server, но это лишь один из многих образов, предлагаемых в Azure. Обратите внимание, что доступные образы зависят от подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов.

Можно также создавать виртуальные машины с помощью [собственных образов](virtual-machines-create-upload-vhd-windows-server.md). Дополнительную информацию об этом и других методах см. в статье [Различные способы создания виртуальной машины Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Как создать виртуальную машину

В этом разделе рассматривается использование метода **Из коллекции** на портале управления Azure для создания виртуальной машины. Указанный вариант предлагает больший выбор настроек, чем метод **Быстрое создание**. Например, при необходимости присоединить виртуальную машину к виртуальной сети следует использовать параметр **Из коллекции**.

> [AZURE.NOTE]У вас также есть возможность использовать более богатый функционально, настраиваемый [портал предварительной версии Azure](https://portal.azure.com) для создания виртуальной машины, автоматизации развертывания шаблонов приложений для нескольких виртуальных машин, применения расширенного мониторинга виртуальных машин, а также диагностических функций и многого другого. Доступные параметры конфигурации ВМ на двух порталах в значительной степени перекрываются, но не полностью.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Дальнейшие действия

- Войдите в виртуальную машину. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server](virtual-machines-log-on-windows-server.md).

- Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Указания см. в [учебнике по подключению диска данных](storage-windows-attach-disk.md).

## Дополнительные ресурсы

Чтоб больше узнать о том, что можно настроить для виртуальной машины и когда это можно сделать, см. раздел [О параметрах конфигурации виртуальной машины Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).
 

<!---HONumber=July15_HO4-->