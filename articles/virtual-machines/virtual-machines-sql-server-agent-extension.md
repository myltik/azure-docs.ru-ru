<properties
	pageTitle="Расширение агента IaaS для SQL Server | Microsoft Azure"
	description="В этом разделе используются ресурсы, созданные с помощью классической модели развертывания, и описывается расширение агента SQL Server, которое позволяет виртуальной машине SQL Server в Azure использовать функции автоматизации."
	services="virtual-machines"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="10/02/2015"
	ms.author="jeffreyg"/>

# Расширение агента IaaS для SQL Server

Это расширение позволяет серверу SQL Server в инфраструктуре виртуальных машинах Azure использовать описанные в этой статье службы, которые будут работать, только если установлено данное расширение. Расширение автоматически устанавливается для образов из коллекции SQL Server на портале Azure, и его можно установить на любую виртуальную машину SQL Server в Azure, на которой установлен гостевой агент виртуальной машины Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


## Предварительные требования
Требования для использования командлетов Powershell:

- последний пакет SDK для командной строки Azure; [доступен здесь](https://azure.microsoft.com/downloads/).

Требования для использования расширения на виртуальной машине:

- гостевой агент виртуальной машины Azure;
- Windows Server 2012, Windows Server 2012 R2 или более поздняя версия;
- SQL Server 2012, SQL Server 2014 или более поздняя версия.

## Доступные после установки расширения службы

- **Служба автоматического резервного копирования SQL**. Эта служба позволяет автоматизировать планирование резервного копирования всех баз данных для установленного на виртуальной машине экземпляра SQL Server по умолчанию. Дополнительные сведения об этой службе см. в статье [Автоматическая архивация для SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-automated-backup.md).
- **Служба автоматической установки исправлений SQL**. Эта служба позволяет настроить период обслуживания, во время которого возможно обновление виртуальной машины. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения об этой службе см. в статье [Автоматическая установка исправлений для SQL Server в инфраструктуре виртуальных машин Azure](virtual-machines-sql-server-automated-patching.md).

## Добавление расширения с помощью Powershell
Если вы подготавливаете виртуальную машину SQL Server с помощью [портала Azure](https://portal.azure.com/), данное расширение будет установлено автоматически. На существующих виртуальных машинах SQL Server, подготовленных через [классический портал Azure](https://manage.windowsazure.com), и виртуальных машинах, на которых вы используете собственную лицензию SQL, это расширение можно добавить с помощью такого командлета Azure PowerShell:

**Set-AzureVMSqlServerExtension**

### Синтаксис

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <AutoBackupSettings>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<CommonParameters>]

> [AZURE.NOTE] Параметр -Version лучше не использовать. Без него по умолчанию используется последняя версия расширения.

### Пример
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Проверка статуса расширения
Для проверки состояния этого расширения и связанных с ним служб можно использовать любой портал. В сведениях о существующей виртуальной машине найдите в разделе **Параметры** пункт **Расширения**.

Вы также можете воспользоваться таким командлетом Azure PowerShell:

**Get-AzureVMSqlServerExtension**

### Синтаксис

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<CommonParameters>]

> [AZURE.NOTE] Параметр -Version можно опустить. Без него по умолчанию используется последняя версия расширения.

### Пример
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Удаление расширения с помощью Powershell   
Чтобы удалить это расширение с виртуальной машины, используйте такой командлет Azure Powershell:

**Remove-AzureVMSqlServerExtension**

### Синтаксис
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=AcomDC_0128_2016-->