---
title: "Автоматизация задач управления на виртуальных машинах SQL (модель с использованием Resource Manager) | Документы Майкрософт"
description: "В этой статье описывается управлять расширением агента SQL Server, который автоматизирует конкретных задач администрирования SQL Server. Эти задачи включают в себя автоматическую архивацию, автоматическую установку исправлений и интеграцию с хранилищем ключей Azure."
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
ms.date: 12/12/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 414c985d21112d658b6e22473f67ed1c3afd00ef
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Автоматизация задач управления на виртуальных машинах Azure с помощью расширения агента SQL Server (модель с использованием Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Классический](../classic/sql-server-agent-extension.md)
> 
> 

Расширение агента IaaS для SQL Server (SQLIaaSExtension) запускается на виртуальных машинах Azure для автоматизации задач администрирования. В этой статье Обзор служб, поддерживаемые расширения, а также инструкции по установке, состояния и удаления.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Версию этой статьи для классической модели развертывания см. здесь: [Расширение агента SQL Server для виртуальных машин SQL Server (классическая модель)](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Поддерживаемые службы
Расширение агента IaaS для SQL Server поддерживает следующие задачи администрирования:

| Функция администрирования | ОПИСАНИЕ |
| --- | --- |
| **Автоматическая архивация SQL** |Автоматизирует планирование архивации всех баз данных для установленного на виртуальной машине экземпляра SQL Server по умолчанию. Дополнительные сведения см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Автоматическая установка исправлений SQL** |Настраивает период обслуживания, во время которого можно обновить виртуальную машину. Таким образом можно избежать установки обновлений в пиковые периоды рабочей нагрузки. Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Интеграция с хранилищем ключей Azure** |Автоматически устанавливает и настраивает хранилище ключей Azure на виртуальной машине SQL Server. Дополнительные сведения см. в статье [Настройка интеграции хранилища ключей Azure для SQL Server на виртуальных машинах Azure (диспетчер ресурсов)](virtual-machines-windows-ps-sql-keyvault.md). |

После установки и запуска, модуль агента IaaS SQL Server создает эти функции администрирования на панели SQL Server виртуальной машины на портале Azure и через Azure PowerShell для SQL Server marketplace образов и Azure PowerShell для установки в ручном режиме расширения. 

## <a name="prerequisites"></a>Технические условия
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

* [Скачайте и настройте последние команды Azure PowerShell](/powershell/azure/overview)

## <a name="installation"></a>Установка
Расширение агента IaaS для SQL Server автоматически устанавливается при подготовке одного из образов коллекции виртуальных машин SQL Server. Если необходимо переустановить расширение вручную на одной из этих виртуальных машин SQL Server, используйте следующую команду PowerShell:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

Можно также установить расширение агента IaaS для SQL Server на виртуальной машине Windows Server, содержащей только операционную систему. Это возможно, если вы вручную установили SQL Server на этом компьютере. После этого вручную установите расширение с помощью того же командлета **Set-AzureVMSqlServerExtension** PowerShell.

> [!NOTE]
> При ручной установке расширения агента IaaS SQL Server на виртуальной машине Windows Server, содержащей только операционную систему, вы не можете управлять параметрами конфигурации SQL Server на портале Azure. В этом случае все изменения нужно вносить с помощью PowerShell.

## <a name="status"></a>Status
Чтобы убедиться, что расширение установлено можно просмотреть состояние агента на портале Azure. Выберите **все параметры** в окне виртуальной машины и затем щелкните на **расширения**. В списке будет указано расширение **SQLIaaSExtension** .

![Расширения агента IaaS SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Можно также использовать **Get AzureVMSqlServerExtension** командлет Azure PowerShell.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Предыдущая команда подтверждает, что агент установлен, и предоставляет общие сведения о состоянии. С помощью следующих команд можно получить определенные сведения о состоянии автоматической архивации и установки исправлений.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Удаление
На портале Azure можно удалить расширение, щелкнув кнопку с многоточием **расширения** окна свойств виртуальной машины. Затем щелкните **Удалить**.

![Удаление расширения агента IaaS SQL Server на портале Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Можно также использовать **AzureRmVMSqlServerExtension удаление** командлета PowerShell.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Дальнейшие действия
Начните работать с одной из служб, поддерживаемых расширением. Дополнительные сведения см. в статьях, на которые ссылается [поддерживается служб](#supported-services) этой статьи.

Подробные сведения о работе SQL Server на виртуальных машинах Azure см. в разделе [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

