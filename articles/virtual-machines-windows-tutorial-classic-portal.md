<properties
	pageTitle="Создание виртуальной машины под управлением Windows в Azure"
	description="Узнайте, как создать виртуальную машину Windows на классическом портале Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-classic-portal"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="kathydav"/>



# Создание виртуальной машины под управлением Windows

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-vms.md)

В этом учебнике показано, насколько просто создавать виртуальные машины Azure (ВМ) под управлением Windows. В учебнике используется образ Windows Server, но это только один из множества образов, доступных в Azure. Это операционные системы Windows, операционные системы на основе Linux и образы с установленными приложениями. Доступные для выбора образы зависят от вашей подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов.

Можно также создавать виртуальные машины Windows с помощью [собственных образов](virtual-machines-create-upload-vhd-windows-server-classic-portal.md). Дополнительную информацию о виртуальных машинах Azure см. в разделе [Общие сведения о виртуальных машинах Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Как создать виртуальную машину

В этом руководстве рассматривается использование метода **Из коллекции** на классическом портале Azure для создания виртуальной машины. Указанный вариант предлагает больший выбор настроек, чем метод **Быстрое создание**. Например, при необходимости присоединить виртуальную машину к виртуальной сети следует использовать параметр **Из коллекции**.

> [AZURE.NOTE]У вас также есть возможность использовать более богатый функционально, настраиваемый [портал Azure](https://portal.azure.com) для создания виртуальной машины, автоматизации развертывания шаблонов приложений для нескольких виртуальных машин, применения расширенного мониторинга виртуальных машин, а также диагностических функций и многого другого. Доступные параметры конфигурации ВМ на двух порталах в значительной степени перекрываются, но не полностью.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## Дальнейшие действия

- Войдите в виртуальную машину. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server](virtual-machines-log-on-windows-server.md).

- Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Указания см. в [учебнике по подключению диска данных](storage-windows-attach-disk.md).

## Дополнительные ресурсы

Чтоб больше узнать о том, что можно настроить для виртуальной машины и когда это можно сделать, см. раздел [О параметрах конфигурации виртуальной машины Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!--HONumber=52-->
