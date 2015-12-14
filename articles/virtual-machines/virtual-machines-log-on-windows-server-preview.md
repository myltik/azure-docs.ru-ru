<properties
	pageTitle="Вход в виртуальную машину Windows Server | Microsoft Azure"
	description="Узнайте, как войти в виртуальную машину Windows Server с помощью портала Azure и модели развертывания на основе диспетчера ресурсов."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Вход в виртуальную машину под управлением Windows Server 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-log-on-windows-server.md).

Для запуска сеанса удаленного рабочего стола используйте кнопку **Подключиться** на портале Azure. Сначала вы подключитесь к виртуальной машине, а затем сможете войти в систему.

## Подключение к виртуальной машине

1. Перейдите на [портал Azure](https://portal.azure.com/), если вы еще этого не сделали.

2.	В главном меню щелкните **Обзор**.

3.	Прокрутите колонку поиска вниз и щелкните **Виртуальные машины**.

	![Поиск виртуальных машин](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	Затем выберите виртуальную машину из списка.

5. В колонке виртуальной машины щелкните **Подключить**.

	![Подключение к виртуальной машине](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Вход на виртуальную машину

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Устранение неполадок

Если советы по входу не помогли устранить проблему или они вам не подходят, см. статью [Устранение неполадок с подключением к удаленному рабочему столу виртуальной машины Windows в службе Azure](virtual-machines-troubleshoot-remote-desktop-connections.md). В ней описывается процесс диагностики и решения распространенных проблем.

<!---HONumber=AcomDC_1203_2015-->