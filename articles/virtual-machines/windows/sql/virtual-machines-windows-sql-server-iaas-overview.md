---
title: Обзор SQL Server на виртуальных машинах Windows в Azure | Документация Майкрософт
description: Из этой статьи вы узнаете, как запускать полные выпуски SQL Server на виртуальных машинах Azure.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/12/2018
ms.author: jroth
ms.openlocfilehash: 6c7f2eb3e126bbcefefe47fa42231527004b2ac5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423105"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Что собой представляет SQL Server на виртуальных машинах Azure (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) позволяет использовать полные версии SQL Server в облаке без необходимости управлять каким-либо локальным оборудованием. Кроме того, оплата по мере использования упрощает лицензирование для виртуальных машин SQL Server.

Виртуальные машины Azure работают во многих [географических регионах](https://azure.microsoft.com/regions/) по всему миру. На выбор предоставляется множество [размеров машин](../sizes.md). Коллекция образов позволяет создать виртуальную машину SQL Server нужной версии и выпуска, а также с требуемой операционной системой. Благодаря этому виртуальные машины — отличный вариант для различных рабочих нагрузок SQL Server.

## <a name="automated-updates"></a>Автоматические обновления

Виртуальные машины Azure SQL Server также поддерживают [автоматическую установку исправлений](virtual-machines-windows-sql-automated-patching.md), функцию, которая позволяет планировать график автоматической установки важных обновлений для Windows и SQL Server.

## <a name="automated-backups"></a>Автоматическое резервное копирование

Виртуальные машины Azure SQL Server поддерживают [автоматическое резервное копирование](virtual-machines-windows-sql-automated-backup-v2.md), при котором в хранилище BLOB-объектов регулярно создаются резервные копии базы данных. Это также можно выполнять вручную. Дополнительные сведения см. в статье [Использование службы хранилища Azure для архивации и восстановления SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>высокую доступность;

Если требуется высокий уровень доступности, вам помогут группы доступности SQL Server. Это подразумевает использование нескольких виртуальных машин Azure SQL Server в виртуальной сети. Можно настроить решения с высоким уровнем доступности вручную или использовать для них шаблоны автоматической настройки на портале Azure. Полный обзор средств для обеспечения высокого уровня доступности см. в статье [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Производительность

Виртуальные машины Azure предлагают другие размеры компьютера в соответствии с различными требованиями рабочих нагрузок. Виртуальные машины SQL также предоставляют автоматическую конфигурацию хранилища, оптимизированную для требований к производительности. Дополнительные сведения о настройке хранилища для виртуальных машин SQL см. в статье [Настройка хранилища для виртуальных машин SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Настройка производительности описана в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Начало работы с виртуальными машинами SQL

Чтобы приступить к работе, выберите образ виртуальной машины SQL Server требуемой версии, выпуска и с нужной операционной системой. В следующих разделах приведены прямые ссылки на портал Azure для получения образов виртуальной машины SQL Server из коллекции.

> [!TIP]
> Дополнительные сведения о ценообразовании на образы SQL см. в [руководстве по выбору ценовой категории для виртуальных машин Azure SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md). 

### <a id="payasyougo"></a> Оплата по мере использования
В следующей таблице представлена матрица для образов SQL Server с оплатой по мере использования.

| Version (версия) | Операционная система | Выпуск |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Сведения о доступных образах виртуальных машин SQL Server на базе Linux см. в статье [Обзор SQL Server на виртуальных машинах Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

### <a id="BYOL"></a> Использование собственной лицензии
Вы также можете использовать собственную лицензию (BYOL). В этом сценарии вы платите только за виртуальную машину и не несете расходы на лицензирование SQL Server.  Использование технологии BYOL со временем позволяет снизить расходы на использование непрерывных производственных рабочих нагрузок. Требования для использования этой возможности см. в [руководстве по выбору ценовой категории для виртуальных машин Azure SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).

| Version (версия) | Операционная система | Выпуск |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

На портале имена таких образов начинаются с префикса **{BYOL}**.

### <a name="connect-to-the-vm"></a>Подключение к виртуальной машине
После создания виртуальной машины SQL Server установите подключение к ней из приложений или средств, таких как SQL Server Management Studio (SSMS). Ознакомьтесь со статьей о [подключении к виртуальной машине SQL Server в Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Перенос данных
Если у вас есть база данных, вам необходимо будет переместить ее в новую виртуальную машину SQL. Список вариантов и руководство по миграции баз данных см. в статье [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md).

## <a id="lifecycle"></a> Политика обновления образа виртуальной машины SQL
Azure поддерживает только один образ виртуальной машины для каждого сочетания поддерживаемой операционной системы, версии и выпуска. Это означает, что со временем образы обновляются, а старые образы удаляются. Дополнительные сведения см. в разделе **Образы** статьи [Часто задаваемые вопросы по SQL Server на виртуальных машинах Azure (Windows)](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Программа улучшения качества программного обеспечения (CEIP)
Программа по улучшению качества программного обеспечения (CEIP) включена по умолчанию. Она периодически отправляет отчеты в Майкрософт, чтобы улучшить качество SQL Server. Если вы не хотите отключить CEIP после подготовки, то дополнительные задания управления не требуются. Вы можете настроить или отключить программу CEIP, подключившись к виртуальной машине с помощью удаленного рабочего стола. Затем запустите служебную программу **Служба отчетов об ошибках и использовании SQL Server** . Следуйте инструкциям в окнах этой программы, чтобы отключить создание отчетов. Дополнительные сведения о сборе данных см. в [заявлении о конфиденциальности SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx).

## <a name="related-products-and-services"></a>Связанные продукты и услуги
### <a name="windows-virtual-machines"></a>Виртуальные машины Windows
* [Обзор виртуальных машин](../overview.md).

### <a name="storage"></a>Хранилище
* [Введение в службу хранилища Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Сеть
* [Обзор виртуальной сети](../../../virtual-network/virtual-networks-overview.md)
* [IP-адреса в Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Создание полного доменного имени на портале Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Документация по SQL Server](https://docs.microsoft.com/sql/index).
* [Сравнение со службой "База данных SQL"](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a name="next-steps"></a>Дополнительная информация

Начало работы с SQL Server на виртуальных машинах Azure:

* [Создание виртуальной машины SQL Server на портале Azure](quickstart-sql-vm-create-portal.md).

Ответы на часто задаваемые вопросы о виртуальных машинах SQL:

* [Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-faq.md)
