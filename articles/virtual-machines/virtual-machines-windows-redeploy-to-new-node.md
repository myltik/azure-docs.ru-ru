<properties 
	pageTitle="Повторное развертывание виртуальных машин Windows | Microsoft Azure" 
	description="Повторное развертывание виртуальных машин Windows для устранения проблем с подключением к удаленному рабочему столу." 
	services="virtual-machines-windows" 
	documentationCenter="virtual-machines" 
	authors="iainfoulds" 
	manager="timlt"
	tags="azure-resource-manager,top-support-issue" 
/>
	

<tags 
	ms.service="virtual-machines-windows" 
	ms.devlang="na" 
	ms.topic="support-article" 
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure" 
	ms.date="09/19/2016" 
	ms.author="iainfou" 
/>


# Повторное развертывание виртуальной машины на новом узле Azure

Если вам не удается подключиться к удаленному рабочему столу или получить доступ к приложению на виртуальной машине Microsoft Azure, можно попробовать повторно развернуть виртуальную машину. При повторном развертывании виртуальная машина перемещается на новый узел в рамках инфраструктуры Azure. Там она снова включается с сохранением всех параметров конфигурации и связанных ресурсов. В этой статье показано, как повторно развернуть виртуальную машину с помощью Azure PowerShell или портала Azure.

> [AZURE.NOTE] После развертывания временный диск будет удален, а связанные с виртуальным сетевым интерфейсом динамические IP-адреса будут обновлены.

## Использование Azure PowerShell

Убедитесь, что на компьютере установлена последняя версия Azure PowerShell 1.x. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Используйте эту команду Azure PowerShell для повторного развертывания виртуальной машины.

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## Дальнейшие действия
При проблемах с подключением к виртуальной машине ознакомьтесь со статьями [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md) и [Подробная диагностика подключений к удаленному рабочему столу виртуальной машины Azure под управлением Windows](virtual-machines-windows-detailed-troubleshoot-rdp.md). При проблемах с доступом к приложению, выполняющемуся в виртуальной машине, ознакомьтесь со статьей [Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](virtual-machines-windows-troubleshoot-app-connection.md).

<!---HONumber=AcomDC_0921_2016-->