<properties
	pageTitle="Расширение агента SQL Server для виртуальных машин SQL Server (Resource Manager) | Microsoft Azure"
	description="В этой статье описывается управление расширением агента SQL Server, которое позволяет автоматизировать выполнение определенных задач администрирования SQL Server. Эти задачи включают в себя автоматическую архивацию, автоматическую установку исправлений и интеграцию с хранилищем ключей Azure. Здесь описывается использование модели развертывания с помощью Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Расширение агента SQL Server для виртуальных машин SQL Server (Resource Manager)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-sql-server-agent-extension.md)
- [Классический](virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SQLIaaSExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этом разделе представлен обзор служб, поддерживаемых расширением, а также указания по установке, проверке состояния и удалению расширения.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания. При необходимости см. статью о классической модели развертывания — [Расширение агента IaaS для SQL Server (классическая модель)](virtual-machines-windows-classic-sql-server-agent-extension.md).

## Поддерживаемые службы

Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
|---------------------|-------------------------------|
| **Автоматическая архивация SQL** | Автоматизирует планирование архивации всех баз данных для установленного на виртуальной машине экземпляра SQL Server по умолчанию. Дополнительные сведения см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-sql-automated-backup.md).|
| **Автоматическая установка исправлений SQL** | Настраивает период обслуживания, во время которого можно обновить виртуальную машину. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическое исправление SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-sql-automated-patching.md).|
| **Интеграция с хранилищем ключей Azure** | Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-ps-sql-keyvault.md).|

## Предварительные требования

Требования для использования расширения агента IaaS для SQL Server на виртуальной машине:

**Операционная система**

- Windows Server 2012
- Windows Server 2012 R2

**Версии SQL Server**

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**

- [Скачайте и настройте последние команды Azure PowerShell](../powershell-install-configure.md)

## Установка

Расширение агента IaaS для SQL Server автоматически устанавливается при подготовке одного из образов коллекции виртуальных машин SQL Server.

При создании виртуальной машины Windows Server только для OС можно установить расширение вручную с помощью командлета PowerShell **Set-AzureVMSqlServerExtension**. Например, следующая команда устанавливает расширение на виртуальной машине только для OС Windows Server и называет ее "SQLIaaSExtension".

	Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

После обновления расширения агента IaaS SQL до последней версии виртуальную машину необходимо перезапустить.

## Состояние

Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. В колонке виртуальной машины выберите **Все параметры**, а затем щелкните **Расширения**. В списке будет указано расширение **SQLIaaSExtension**.

![Расширение агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Можно также использовать командлет Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Предыдущая команда подтверждает, что агент установлен, и предоставляет общие сведения о состоянии. С помощью следующих команд можно получить определенные сведения о состоянии автоматической архивации и установки исправлений.

	$sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
	$sqlext.AutoPatchingSettings
	$sqlext.AutoBackupSettings

## Удаление   

Чтобы удалить расширение на портале Azure, в колонке **Расширения** в свойствах виртуальной машины щелкните многоточие, а затем выберите **Удалить**.

![Удаление расширения агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Можно также использовать командлет PowerShell **Remove-AzureRmVMSqlServerExtension**.

	Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## Дальнейшие действия

Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в разделе [Поддерживаемые службы](#supported-services) в этой статье.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в разделе [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0525_2016-->