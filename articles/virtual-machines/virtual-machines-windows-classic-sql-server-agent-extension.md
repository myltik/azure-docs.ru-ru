<properties
	pageTitle="Расширение агента IaaS для SQL Server (классическая модель) | Microsoft Azure"
	description="В этом разделе описывается расширение агента SQL Server, которое позволяет виртуальной машине SQL Server в Azure использовать функции автоматизации. В разделе используется классическая модель развертывания."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
   editor=""    
   tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/08/2016"
	ms.author="jroth"/>

# Расширение агента IaaS для SQL Server \(классическая модель\)

Это расширение позволяет серверу SQL Server в инфраструктуре виртуальных машинах Azure использовать описанные в этой статье службы, которые будут работать, только если установлено данное расширение. Расширение автоматически устанавливается для образов из коллекции SQL Server на портале Azure, и его можно установить на любую виртуальную машину SQL Server в Azure, на которой установлен гостевой агент виртуальной машины Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


## Предварительные требования
Требования для использования командлетов Powershell:

- Последняя версия Azure PowerShell [доступна здесь](../powershell-install-configure.md).

Требования для использования расширения на виртуальной машине:

- гостевой агент виртуальной машины Azure;
- Windows Server 2012, Windows Server 2012 R2 или более поздняя версия;
- SQL Server 2012, SQL Server 2014 или более поздняя версия.

## Доступные после установки расширения службы

- **Служба автоматической архивации SQL**. Эта служба позволяет автоматизировать планирование архивации всех баз данных для установленного на виртуальной машине экземпляра SQL Server по умолчанию. Дополнительную информацию см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure \(классическая модель\)](virtual-machines-windows-classic-sql-automated-backup.md).
- **Служба автоматической установки исправлений SQL**. Эта служба позволяет настроить период обслуживания, во время которого возможно обновление виртуальной машины. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure \(классическая модель\)](virtual-machines-windows-classic-sql-automated-patching.md).
- **Интеграции хранилища ключей Azure**. Эта служба позволяет автоматически установить и настроить хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure \(классическая модель\)](virtual-machines-windows-classic-ps-sql-keyvault.md).

## Добавление расширения с помощью Powershell
Если вы подготавливаете виртуальную машину SQL Server с помощью [портала Azure](virtual-machines-windows-portal-sql-server-provision.md), данное расширение будет установлено автоматически. На виртуальных машинах SQL Server, подготовленных с помощью классического портала Azure, или виртуальных машинах, на которых вы используете собственную лицензию SQL, это расширение можно добавить с помощью командлета Azure PowerShell **Set-AzureVMSqlServerExtension**.

### Синтаксис

Set-AzureVMSqlServerExtension [[-ReferenceName\] \[String]] \[-VM\] IPersistentVM [[-Version\] \[String]] [[-AutoPatchingSettings\] \[AutoPatchingSettings]] \[-AutoBackupSettings\[AutoBackupSettings\]\] \[-Profile \[AzureProfile\]\] \[CommonParameters\]

> [AZURE.NOTE] Параметр -Version лучше не использовать. Без него по умолчанию используется последняя версия расширения.

### Пример
В следующем примере настраиваются параметры автоматической архивации с помощью конфигурации, определенной в $abs \(здесь не показана\). ServiceName — имя облачной службы, в которой размещается виртуальная машина. Полный пример см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure \(классическая модель\)](virtual-machines-windows-classic-sql-automated-backup.md).

	Get-AzureVM –ServiceName "serviceName" –Name "vmName" | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Проверка статуса расширения
Для проверки состояния этого расширения и связанных с ним служб можно использовать любой портал. В сведениях о существующей виртуальной машине найдите в разделе **Параметры** пункт **Расширения**.

Можно также использовать командлет Azure PowerShell **Get-AzureVMSqlServerExtension**.

### Синтаксис

Get-AzureVMSqlServerExtension [[-VM\] \[IPersistentVM]] \[-Profile \[AzureProfile\]\] \[CommonParameters\]

### Пример
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Удаление расширения с помощью Powershell   
Чтобы удалить это расширение с виртуальной машины, используйте командлет Azure PowerShell **Remove-AzureVMSqlServerExtension**.

### Синтаксис

Remove-AzureVMSqlServerExtension \[-Profile \[AzureProfile\]\] -VM IPersistentVM \[CommonParameters\]

<!---HONumber=AcomDC_0413_2016-->