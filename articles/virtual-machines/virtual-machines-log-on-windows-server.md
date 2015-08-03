<properties 
	pageTitle="Вход в виртуальную машину под управлением Windows Server" 
	description="Узнайте, как использовать портал управления Azure для входа в виртуальную машину под управлением Windows Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="kathydav"/>


# Как войти в виртуальную машину под управлением Windows Server#

Для запуска сеанса удаленного рабочего стола используйте кнопку **Подключиться** на портале Azure. (Дополнительную информацию см. в разделе [Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md).)

## Как войти в систему

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Советы по устранению неполадок

Вот несколько быстрых способов.

Если возникли проблемы с подключением к удаленному рабочему столу, сбросьте конфигурацию на портале. На панели мониторинга виртуальной машины в разделе **Сводка** щелкните **Сброс удаленной конфигурации**.

Если возникли проблемы с паролем, попробуйте сбросить его на портале. На панели мониторинга виртуальной машины в разделе **Сводка** щелкните **Сбросить пароль**.

Если это не сработает, необходимо выполнить более масштабные действия по устранению неполадок. Инструкции см. в статье [Устранение неполадок с подключением к удаленному рабочему столу виртуальной машины Windows в службе Azure](virtual-machines-troubleshoot-remote-desktop-connections.md).
 
 

<!---HONumber=July15_HO4-->