---
title: "Автоматическая архивация для виртуальных машин SQL Server 2014 в Azure | Документация Майкрософт"
description: "Описывает функцию автоматической архивации для виртуальных машин SQL Server 2014 в Azure. Данная статья относится к виртуальным машинам, использующим модель развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/30/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 253c504fa433c7ca37c0065ebf01d13dafc76231
ms.openlocfilehash: c4cf6ab29ebf5b3397017cf754ee04bf57ab1555


---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Автоматическая архивация для виртуальных машин SQL Server 2014 (Resource Manager)
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов](virtual-machines-windows-sql-automated-backup.md)
> * [Классический](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md)
> 
> 

Служба автоматической архивации автоматически настраивает [управляемое резервное копирование на портал Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) для всех существующих и новых баз данных на виртуальной машине Azure c SQL Server 2014 Standard или Enterprise. Это позволяет настроить регулярную архивацию базы данных с использованием надежного хранилища больших двоичных объектов Azure. Автоматическая архивация зависит от [Расширения агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Версию этой статьи для классической модели развертывания см. здесь: [Автоматическая архивация SQL Server на виртуальных машинах Azure (классическая модель)](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Предварительные требования
Для использования автоматической архивации необходимо выполнить следующие предварительные требования.

**Операционная система**

- Windows Server 2012
- Windows Server 2012 R2

**Версия/выпуск SQL Server**

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Автоматическая архивация работает с SQL Server 2014. Если используется SQL Server 2016, то для архивации баз данных можно применять автоматическую архивацию версии 2. Дополнительную информацию см. в статье [Автоматическая архивация версии 2 для виртуальных машин SQL Server 2016 в Azure](virtual-machines-windows-sql-automated-backup-v2.md).

**Конфигурация базы данных**

- В конечных базах данных должна использоваться модель полного восстановления.

Дополнительные сведения о влиянии модели полного восстановления на резервные копии см. в разделе [Резервное копирование в модели полного восстановления](https://technet.microsoft.com/library/ms190217.aspx).

**Модель развертывания Azure**

- Диспетчер ресурсов

**Azure PowerShell**

- [Установите последнюю версию Azure PowerShell](/powershell/azureps-cmdlets-docs) , если планируете настраивать автоматизированную архивацию с помощью PowerShell.

> [!NOTE]
> Автоматическая архивация зависит от расширения агента IaaS для SQL Server. В текущей коллекции образов виртуальных машин SQL это расширение присутствует по умолчанию. Дополнительные сведения см. в статье [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) (Расширение агента SQL Server IaaS).

## <a name="settings"></a>Параметры
В приведенной ниже таблице описаны параметры настройки автоматической архивации. Фактическая процедура настройки может варьироваться в зависимости от того, используете вы портал Azure или команды Azure Windows PowerShell.

| Настройка | Диапазон (по умолчанию) | Описание |
| --- | --- | --- |
| **Автоматическая архивация** | Включено/отключено (отключено) | Включает или отключает автоматическую архивацию для виртуальной машины Azure под управлением SQL Server 2014 Standard или Enterprise. |
| **Срок хранения** | 1–30 дней (30 дней) | Число дней хранения резервной копии. |
| **Учетная запись хранения** | Учетная запись хранения Azure. | Учетная запись хранения Azure для хранения файлов автоматической архивации в хранилище больших двоичных объектов. Там же создается контейнер для хранения всех файлов резервных копий. Имя файла резервной копии содержит дату, время и имя компьютера. |
| **Шифрование** | Включено/отключено (отключено) | Включает или отключает шифрование. Если шифрование включено, то сертификаты, используемые для восстановления резервной копии, сохраняются в указанной учетной записи хранения в том же контейнере `automaticbackup` с использованием того же соглашения об именовании файлов. При изменении пароля создается новый сертификат; при этом старый сертификат сохраняется для восстановления предыдущих резервных копий. |
| **Пароль** | Текст пароля | Пароль для ключей шифрования. Требуется, только если шифрование включено. Для восстановления зашифрованной резервной копии требуется правильный пароль и соответствующий сертификат, который использовался при создании резервной копии. |

## <a name="configuration-in-the-portal"></a>Настройка на портале
Для настройки автоматической архивации при подготовке виртуальных машин SQL Server 2014 или для существующих виртуальных машин SQL Server 2016 можно использовать портал Azure.

### <a name="new-vms"></a>Новые виртуальные машины
При создании новой виртуальной машины SQL Server 2014 с моделью развертывания с помощью Resource Manager настройте автоматическую архивацию, используя портал Azure.

В колонке **Параметры SQL Server** выберите **Автоматизированная архивация**. Колонка **Автоматизированная архивация SQL** показана на следующем снимке экрана портала Azure.

![Настройка автоматической архивации SQL на портале Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Сведения о контексте приведены в полном описании в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Существующие виртуальные машины
Выберите существующую виртуальную машину SQL Server. Затем в колонке **Параметры** выберите раздел **Конфигурация SQL Server**.

![Автоматизированная архивация SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

В колонке **Конфигурация SQL Server** нажмите кнопку **Изменить** в разделе "Автоматизированная архивация".

![Настройка автоматизированной архивации SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

По завершении нажмите кнопку **ОК** в нижней части колонки **Конфигурация SQL Server**, чтобы сохранить изменения.

Если автоматизированная архивация включается впервые, Azure настроит агент IaaS SQL Server в фоновом режиме. При этом портал Azure может не сообщать о том, что выполняется настройка автоматической архивации. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале Azure.

> [!NOTE]
> Можно также настроить автоматизированную архивацию с помощью шаблона. Чтобы получить дополнительные сведения, ознакомьтесь с [шаблоном быстрого запуска Azure для автоматизированной архивации](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).
> 
> 

## <a name="configuration-with-powershell"></a>Настройка с помощью PowerShell
После подготовки виртуальной машины SQL используйте PowerShell для настройки автоматической архивации. Предварительно необходимо выполнить следующее.

- [Скачайте и установите последнюю версию Azure PowerShell](http://aka.ms/webpi-azps).
- Откройте компонент Windows PowerShell и свяжите его со своей учетной записью. Это можно сделать, выполнив действия, описанные в посвященном подготовке разделе [Настройка подписки](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription).

В следующем примере с использованием PowerShell автоматическая архивация настраивается для существующей виртуальной машины SQL Server 2014. Команда **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** настраивает параметры автоматизированной архивации для хранения архивов в учетной записи хранения Azure, связанной с виртуальной машиной. Эти резервные копии будут храниться в течение 10 дней. Команда **Set-AzureRmVMSqlServerExtension** обновляет указанную виртуальную машину Azure в соответствии с заданными параметрами.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы включить шифрование, измените предыдущий сценарий таким образом, чтобы он передавал параметр **EnableEncryption** вместе с паролем (защищенной строкой) для параметра **CertificatePassword**. Следующий скрипт активирует параметры автоматической архивации их предыдущего примера и добавляет шифрование.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Чтобы отключить автоматизированную архивацию, выполните тот же сценарий без параметра **-Enable** команды **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig**. Отсутствие параметра **-Enable** означает, что функцию нужно отключить. Как и установка, отключение автоматической архивации занимает несколько минут.

> [!NOTE]
> При удалении агента IaaS для SQL Server заданные ранее параметры автоматической архивации не удаляются. Перед отключением и удалением агента SQL Server IaaS необходимо отключить автоматическую архивацию.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Автоматическая архивация настраивает управляемое резервное копирование на виртуальных машинах Azure. В связи с этим важно изучить [документацию по управляемой архивации](https://msdn.microsoft.com/library/dn449496.aspx) и понять, как она работает.

Дополнительные сведения об архивации и восстановлении SQL Server на виртуальных машинах Azure см. в статье [Архивация и восстановление SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).




<!--HONumber=Jan17_HO5-->


