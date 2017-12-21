---
title: "Обзор SQL Server на виртуальных машинах Linux в Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как запускать полные выпуски SQL Server на виртуальных машинах Linux в Azure. Прямые ссылки на все образы виртуальных машин SQL Server на базе Linux и соответствующие статьи."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: f044fcba92eb7aa7f4ac44608571fab3db0e03f5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Обзор SQL Server на виртуальных машинах Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

В этой статье описано, как использовать SQL Server на виртуальных машинах Linux в Azure, а также приведены [ссылки на образы портала](#option-1-create-a-sql-vm-with-per-minute-licensing).

> [!NOTE]
> Если вы уже знакомы с решением SQL Server и просто хотите узнать, как развертывать виртуальную машину SQL Server на базе Linux, см. статью [Подготовка виртуальной машины SQL Server на базе Linux на портале Azure](provision-sql-server-linux-virtual-machine.md). Если же вы хотите создать виртуальную машину Windows с помощью SQL Server, см. статью [Подготовка виртуальной машины SQL Server на портале Azure](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Виртуальные машины Azure предоставляют разработчикам или администраторам базы данных возможность перенести локальные рабочие нагрузки и приложения SQL Server в облако.

## <a name="scenarios"></a>Сценарии

Существует множество причин для размещения данных в Azure. Если вы разрабатываете или переносите свое приложение в Azure, вы получаете повышенную производительность за счет размещения серверных данных в Azure. Вы автоматически получаете доступ ко множеству центров обработки данных, что позволяет обеспечить глобальное присутствие и аварийное восстановление. Кроме того, данные надежно защищены и хранятся на протяжении длительного срока.

Выполнение SQL Server на виртуальной машине Azure — один из вариантов хранения реляционных данных в Azure. Вы также можете воспользоваться службой базы данных SQL Azure. Дополнительные сведения о выборе между SQL Server на виртуальных машинах или в базе данных SQL Azure см. в статье [Вы можете выбрать компонент SQL Server в облаке: база данных SQL Azure (PaaS) или SQL Server на виртуальных машинах Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a id="create"></a> Создание виртуальной машины SQL

Пошаговое руководство по созданию виртуальной машины SQL см. в статье [Подготовка виртуальной машины SQL Server на базе Linux на портале Azure](provision-sql-server-linux-virtual-machine.md).

Следующая таблица содержит матрицу последних образов SQL Server в коллекции виртуальных машин. Выберите необходимые версию, выпуск и операционную систему и щелкните соответствующую ссылку, чтобы начать создание виртуальной машины SQL.

> [!TIP]
> Сведения о ценах на виртуальные машины SQL Server на базе Linux для этих образов см. на [этой странице](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version (версия) | Операционная система | Выпуск |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) версии 12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Сведения о доступных образах виртуальных машин SQL Server на базе Windows см. в статье [Приступая к работе с SQL Server в виртуальных машинах Azure](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Установленные пакеты

При настройке SQL Server в Linux установите пакет ядра СУБД, а затем некоторые дополнительные пакеты, в зависимости от ваших целей. Образы виртуальных машин Linux для SQL Server автоматически устанавливают большинство пакетов. В следующей таблице показаны установленные пакеты для каждого дистрибутива.

| Дистрибутив | [Ядро СУБД](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Инструменты](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Агент SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Полнотекстовый поиск](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Надстройка для обеспечения высокого уровня доступности](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Нет](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Нет](./media/sql-server-linux-virtual-machines-overview/no.png) | ![Нет](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о настройке и использовании SQL Server на базе Linux см. в [этой статье](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
