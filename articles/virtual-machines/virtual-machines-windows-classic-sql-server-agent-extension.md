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
    ms.date="10/27/2016"
    ms.author="jroth"/>


# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>Расширение агента SQL Server для виртуальных машин SQL Server (классическая модель)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-sql-server-agent-extension.md)
- [Классический](virtual-machines-windows-classic-sql-server-agent-extension.md)

Расширение агента IaaS для SQL Server (SQLIaaSAgent) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этом разделе представлен обзор служб, поддерживаемых расширением, а также указания по установке, проверке состояния и удалению расширения.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Чтобы просмотреть версию этой статьи для модели Resource Manager, см. статью [Расширение агента IaaS для виртуальных машин SQL Server (Resource Manager)](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Поддерживаемые службы

Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
|---------------------|-------------------------------|
| **Автоматическая архивация SQL** | Автоматизирует планирование архивации всех баз данных для установленного на виртуальной машине экземпляра SQL Server по умолчанию. Дополнительные сведения см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (классическая модель)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Автоматическая установка исправлений SQL** | Настраивает период обслуживания, во время которого можно обновить виртуальную машину. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (классическая модель)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Интеграция с хранилищем ключей Azure** | Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (классическая модель)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Предварительные требования

Требования для использования расширения агента IaaS для SQL Server на виртуальной машине:

### <a name="operating-system"></a>Операционная система:

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>Версии SQL Server:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell

[Скачайте и настройте последнюю версию команд Azure PowerShell](../powershell-install-configure.md).

Запустите оболочку Windows PowerShell и подключите ее к своей подписке Azure с помощью команды **Add-AzureAccount** .

    Add-AzureAccount

Если у вас несколько подписок, воспользуйтесь командой **Select-AzureSubscription** , чтобы выбрать подписку, которая содержит целевую виртуальную машину, развернутую с помощью классической модели.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

На этом этапе вы можете получить список классических виртуальных машин и имена связанных служб с помощью команды **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Установка

Для установки расширения агента IaaS SQL Server и настройки связанных служб на классических виртуальных машинах используется PowerShell. Установите расширение с помощью командлета PowerShell **Set-AzureVMSqlServerExtension** . Например, следующая команда устанавливает расширение на виртуальную машину Windows Server (классической модели) и присваивает ему имя SQLIaaSExtension.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

После обновления расширения агента IaaS SQL до последней версии виртуальную машину необходимо перезапустить.

>[AZURE.NOTE] В классических виртуальных машинах не предусмотрена функция установки и настройки расширения агента IaaS SQL через портал.

## <a name="status"></a>Состояние

Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. В колонке виртуальной машины выберите **Все параметры**, а затем щелкните **Расширения**. В списке будет указано расширение **SQLIaaSAgent** .

![Расширение агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Можно также использовать командлет Azure PowerShell **Get-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Удаление   

Чтобы удалить расширение на портале Azure, в колонке **Расширения** в свойствах виртуальной машины щелкните многоточие. Затем щелкните **Удалить**.

![Удаление расширения агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Вы также можете использовать командлет PowerShell **Remove-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Дальнейшие действия

Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в разделе [Поддерживаемые службы](#supported-services) этой статьи.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в разделе [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).



<!--HONumber=Oct16_HO2-->


