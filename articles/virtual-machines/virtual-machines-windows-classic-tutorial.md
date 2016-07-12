<properties
	pageTitle="Создание виртуальной машины под управлением Windows на классическом портале | Microsoft Azure"
	description="Информация о создании виртуальной машины под управлением Windows на классическом портале Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="cynthn"/>

# Создание виртуальной машины под управлением Windows на классическом портале Azure

> [AZURE.SELECTOR]
- [Портал Azure](virtual-machines-windows-hero-tutorial.md)
- [Классический портал Azure](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: развертывание Resource Manager](virtual-machines-windows-ps-manage.md)
- [PowerShell: классическое развертывание](virtual-machines-windows-classic-create-powershell.md)

<!-- HHTML comment in to break between the selector and the note in the include below-->

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия для модели развертывания с помощью Resource Manager](virtual-machines-windows-hero-tutorial.md).

В этом учебнике показано, как просто создать виртуальную машину Azure под управлением Windows на классическом портале Azure. Мы будем использовать в качестве примера образ Windows Server, но это лишь один из многих образов, предлагаемых в Azure. Обратите внимание, что доступные образы зависят от подписки. Например, подписчикам MSDN могут быть доступны образы рабочих столов Windows.


Можно также создавать виртуальные машины с помощью [собственных образов](virtual-machines-windows-classic-createupload-vhd.md). Дополнительную информацию об этом и других методах см. в статье [Различные способы создания виртуальной машины Windows](virtual-machines-windows-creation-choices.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Видеоруководство

Ниже приведено видео с пошаговым выполнением действий по этому учебнику.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Как создать виртуальную машину

В этом руководстве рассматривается использование метода **Из коллекции** на классическом портале Azure для создания виртуальной машины. Указанный вариант предлагает больший выбор настроек, чем метод **Быстрое создание**. Например, при необходимости присоединить виртуальную машину к виртуальной сети следует использовать параметр **Из коллекции**.

> [AZURE.NOTE] На более функциональном и настраиваемом портале Azure можно также создавать виртуальные машины, применять расширенные функции мониторинга и диагностики виртуальных машин, использовать хранилище Premium и делать многое другое. Доступные параметры конфигурации виртуальных машин на двух порталах в значительной степени одинаковы, но не полностью. Например, на портале Azure можно настроить виртуальную машину с хранилищем класса Premium.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Дальнейшие действия

- Войдите в виртуальную машину. Инструкции см. в статье [Вход в виртуальную машину под управлением Windows с помощью классического портала Azure](virtual-machines-windows-classic-connect-logon.md).

- Подключите диск для хранения данных. Можно присоединять пустые диски и диски с данными. Инструкции см. в статье [Подключение диска данных к виртуальной машине Windows, созданной с использованием классической модели развертывания](virtual-machines-windows-classic-attach-disk.md).

<!---HONumber=AcomDC_0629_2016-->