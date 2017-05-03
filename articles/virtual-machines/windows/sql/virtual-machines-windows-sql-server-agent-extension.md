---
title: "Автоматизация задач управления на виртуальных машинах SQL (модель с использованием Resource Manager) | Документы Майкрософт"
description: "В этой статье описывается управление расширением агента SQL Server, которое позволяет автоматизировать выполнение определенных задач администрирования SQL Server. Эти задачи включают в себя автоматическую архивацию, автоматическую установку исправлений и интеграцию с хранилищем ключей Azure. Здесь описывается использование модели развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/24/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 27064b6b5c65e5ecd0ef6931bd8f333469419839
ms.lasthandoff: 04/26/2017

---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента SQL Server (модель с использованием Resource Manager)
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов](virtual-machines-windows-sql-server-agent-extension.md)
> * [Классический](../classic/sql-server-agent-extension.md)
> 
> 

Расширение агента IaaS для SQL Server (SQLIaaSExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этом разделе представлен обзор служб, поддерживаемых расширением, а также указания по установке, проверке состояния и удалению расширения.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Версию этой статьи для классической модели развертывания см. здесь: [Расширение агента SQL Server для виртуальных машин SQL Server (классическая модель)](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Поддерживаемые службы
Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | Описание |
| --- | --- |
| **Автоматическая архивация SQL** |Автоматизирует планирование архивации всех баз данных для установленного на виртуальной машине экземпляра SQL Server по умолчанию. Дополнительные сведения см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Автоматическая установка исправлений SQL** |Настраивает период обслуживания, во время которого можно обновить виртуальную машину. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Интеграция с хранилищем ключей Azure** |Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Предварительные требования
Требования для использования расширения агента IaaS для SQL Server на виртуальной машине:

**Операционная система**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Версии SQL Server**

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**

* [Скачайте и настройте последние команды Azure PowerShell](/powershell/azureps-cmdlets-docs)

## <a name="installation"></a>Установка
Расширение агента IaaS для SQL Server автоматически устанавливается при подготовке одного из образов коллекции виртуальных машин SQL Server.

Можно также установить расширение агента IaaS для SQL Server на виртуальной машине Windows Server, содержащей только операционную систему. Это возможно, если вы вручную установили SQL Server на этом компьютере. После этого вручную установите расширение с помощью командлета **Set-AzureVMSqlServerExtension** PowerShell. Например, следующая команда устанавливает расширение на виртуальной машине только для OС Windows Server и называет ее "SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"

> [!NOTE]
> При ручной установке расширения агента IaaS SQL Server вы не можете управлять параметрами конфигурации SQL Server на портале Azure. В этом случае все изменения нужно вносить с помощью PowerShell.

После обновления расширения агента IaaS SQL до последней версии виртуальную машину необходимо перезапустить.

## <a name="status"></a>Состояние
Одним из способов проверки того, что расширение установлено, является просмотр состояния агента на портале Azure. В колонке виртуальной машины выберите **Все параметры**, а затем щелкните **Расширения**. В списке будет указано расширение **SQLIaaSExtension** .

![Расширение агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Можно также использовать командлет Azure PowerShell **Get-AzureVMSqlServerExtension** .

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Предыдущая команда подтверждает, что агент установлен, и предоставляет общие сведения о состоянии. С помощью следующих команд можно получить определенные сведения о состоянии автоматической архивации и установки исправлений.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Удаление
Чтобы удалить расширение на портале Azure, в колонке **Расширения** в свойствах виртуальной машины щелкните многоточие. Затем щелкните **Удалить**.

![Удаление расширения агента IaaS для SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Можно также использовать командлет PowerShell **Remove-AzureRmVMSqlServerExtension** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Дальнейшие действия
Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в разделе [Поддерживаемые службы](#supported-services) этой статьи.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в разделе [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).


