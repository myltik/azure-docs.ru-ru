---
title: "Автоматизированная архивация для виртуальных машин SQL Server (классическая модель) | Документация Майкрософт"
description: "Описание функции автоматической архивации для SQL Server на виртуальных машинах Azure с использованием модели развертывания Resource Manager. "
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 9e7cbf383eff5344f652024606164c9cf264015c
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Автоматическая архивация SQL Server на виртуальных машинах Azure (классическая модель)
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Классический](../classic/sql-automated-backup.md)
> 
> 

Служба автоматической архивации автоматически настраивает [управляемое резервное копирование на портал Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) для всех существующих и новых баз данных на виртуальной машине Azure c SQL Server 2014 Standard или Enterprise. Это позволяет настроить регулярную архивацию базы данных с использованием надежного хранилища больших двоичных объектов Azure. Автоматическая архивация зависит от [Расширения агента IaaS для SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Версию этой статьи для Resource Manager см. в статье [Автоматическая архивация SQL Server на виртуальных машинах Azure (Resource Manager)](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Предварительные требования
Для использования автоматической архивации необходимо выполнить следующие предварительные требования.

**Операционная система**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Версия/выпуск SQL Server**

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Автоматическая архивация пока не поддерживает SQL Server 2016.
> 
> 

**Конфигурация базы данных**

* В конечных базах данных должна использоваться модель полного восстановления.

**Azure PowerShell**

* [Установите последнюю версию команд Azure PowerShell](/powershell/azure/overview).

**Расширение IaaS для SQL Server**

* [Установите расширение IaaS для SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Параметры
В приведенной ниже таблице описаны параметры настройки автоматической архивации. Для виртуальных машин, развернутых с использованием классической модели, эти параметры необходимо настроить с помощью PowerShell.

| Настройка | Диапазон (по умолчанию) | Описание |
| --- | --- | --- |
| **Автоматическая архивация** |Включено/отключено (отключено) |Включает или отключает автоматическую архивацию для виртуальной машины Azure под управлением SQL Server 2014 Standard или Enterprise. |
| **Срок хранения** |1–30 дней (30 дней) |Число дней хранения резервной копии. |
| **Учетная запись хранения** |Учетная запись хранения Azure (учетная запись хранения, созданная для указанной виртуальной машины) |Учетная запись хранения Azure для хранения файлов автоматической архивации в хранилище больших двоичных объектов. Там же создается контейнер для хранения всех файлов резервных копий. Имя файла резервной копии содержит дату, время и имя компьютера. |
| **Шифрование** |Включено/отключено (отключено) |Включает или отключает шифрование. Если шифрование включено, сертификаты, используемые для восстановления резервной копии, сохраняются в указанной учетной записи хранения в том же контейнере automaticbackup с использованием того же принципа именования файлов. При изменении пароля создается новый сертификат; при этом старый сертификат сохраняется для восстановления предыдущих резервных копий. |
| **Пароль** |Текст пароля (нет) |Пароль для ключей шифрования. Требуется, только если шифрование включено. Для восстановления зашифрованной резервной копии требуется правильный пароль и соответствующий сертификат, который использовался при создании резервной копии. | **Архивация системных баз данных** | Включено/отключено (отключено) | Полная архивация Master, Model и MSDB |
| **Настройка расписания архивации баз данных** | Ручная или автоматическая (автоматическая) | Выберите **Автоматическая**, чтобы включить автоматическое создание полных резервных копий и резервных копий журналов в зависимости от размера журнала. Выберите **Ручная**, чтобы задать расписание для создания полных резервных копий и резервных копий журналов. |

## <a name="configuration-with-powershell"></a>Настройка с помощью PowerShell
В следующем примере с использованием PowerShell автоматическая архивация настраивается для существующей виртуальной машины SQL Server 2014. Команда **AzureVMSqlServerAutoBackupConfig** настраивает параметры автоматической архивации для хранения резервных копий в учетной записи хранения Azure, указанной в переменной $storageaccount. Эти резервные копии будут храниться в течение 10 дней. Команда **AzureVMSqlServerExtension** обновляет указанную виртуальную машину Azure в соответствии с заданными параметрами.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы включить шифрование, измените предыдущий скрипт таким образом, чтобы он передавал параметр EnableEncryption вместе с паролем (защищенной строкой) для параметра CertificatePassword. Следующий скрипт активирует параметры автоматической архивации их предыдущего примера и добавляет шифрование.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Чтобы отключить автоматическую архивацию, выполните тот же сценарий без параметра **-Enable** в командлете **New-AzureVMSqlServerAutoBackupConfig**. Как и установка, отключение автоматической архивации занимает несколько минут.

> [!NOTE]
> При отключении и удалении агента SQL Server IaaS не удаляются настроенные ранее параметры управляемого резервного копирования. Перед отключением и удалением агента SQL Server IaaS необходимо отключить автоматическую архивацию.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Автоматическая архивация настраивает управляемое резервное копирование на виртуальных машинах Azure. В связи с этим важно изучить [документацию по управляемой архивации](https://msdn.microsoft.com/library/dn449496.aspx) и понять, как она работает.

Дополнительные сведения об архивации и восстановлении SQL Server на виртуальных машинах Azure см. в статье [Архивация и восстановление SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](../classic/sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).


