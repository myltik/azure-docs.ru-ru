<properties
	pageTitle="Расширение агента SQL Server для виртуальных машин SQL Server (классическая модель) | Microsoft Azure"
	description="В этой статье описывается управление расширением агента SQL Server, которое позволяет автоматизировать выполнение определенных задач администрирования SQL Server. Эти задачи включают в себя автоматическую архивацию, автоматическую установку исправлений и интеграцию с хранилищем ключей Azure. В этом разделе используется классическая модель развертывания."
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
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Расширение агента SQL Server для виртуальных машин SQL Server (классическая модель)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-sql-server-agent-extension.md)
- [Классический](virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SQLIaaSAgent) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этом разделе представлен обзор служб, поддерживаемых расширением, а также указания по установке, проверке состояния и удалению расширения.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]  
Чтобы просмотреть версию этой статьи для модели Resource Manager, см. статью [SQL Server Agent Extension for SQL Server VMs Resource Manager](virtual-machines-windows-sql-server-agent-extension.md) (Расширение агента IaaS для виртуальных машин SQL Server (Resource Manager)).

## Поддерживаемые службы

Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
|---------------------|-------------------------------|
| **Автоматическая архивация SQL** | Автоматизирует планирование архивации всех баз данных для установленного на виртуальной машине экземпляра SQL Server по умолчанию. Дополнительные сведения см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (классическая модель)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Автоматическая установка исправлений SQL** | Настраивает период обслуживания, во время которого можно обновить виртуальную машину. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (классическая модель)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Интеграция с хранилищем ключей Azure** | Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (классическая модель)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

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

**Гостевой агент виртуальной машины**:

- расширение BGInfo устанавливается на новых виртуальных машинах Azure автоматически.

## Установка

Расширение агента IaaS для SQL Server автоматически устанавливается при подготовке одного из образов коллекции виртуальных машин SQL Server.

При создании виртуальной машины Windows Server только для OС можно установить расширение вручную с помощью командлета PowerShell **Set-AzureVMSqlServerExtension**. Используйте команду, чтобы настроить одну из служб агента, например автоматическую установку исправлений. Виртуальная машина устанавливает агент, если он не установлен. Указания по использованию командлета PowerShell **Set-AzureVMSqlServerExtension** см. в отдельных подразделах раздела [Поддерживаемые службы](#supported-services) в этой статье.

После обновления расширения агента IaaS SQL до последней версии виртуальную машину необходимо перезапустить.

>[AZURE.NOTE] Если расширение агента IaaS SQL установлено на виртуальной машине вручную, для использования его функций и управления ими необходимо использовать команды PowerShell. В этом случае интерфейс портала недоступен.

## Состояние

Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. В колонке виртуальной машины выберите **Все параметры**, а затем щелкните **Расширения**. В списке будет указано расширение **SQLIaaSAgent**.

![Расширение агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Можно также использовать командлет Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Удаление   

Чтобы удалить расширение на портале Azure, в колонке **Расширения** в свойствах виртуальной машины щелкните многоточие, а затем выберите **Удалить**.

![Удаление расширения агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Вы также можете использовать командлет PowerShell **Remove-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Дальнейшие действия

Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в разделе [Поддерживаемые службы](#supported-services) в этой статье.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в разделе [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0629_2016--->