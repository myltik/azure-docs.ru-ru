---
title: "Обзор SQL Server на виртуальных машинах Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как запускать полные выпуски SQL Server на виртуальных машинах Azure. Прямые ссылки на все образы виртуальных машин SQL Server и соответствующие статьи."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fcc2883ae52bb3d4be098ce741bbf5f5047777d2


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Приступая к работе с SQL Server в виртуальных машинах Azure
В этой статье описано, как использовать SQL Server на виртуальных машинах Azure, а также приведены [ссылки на образы портала](#option-1-create-a-sql-vm-with-per-minute-licensing) и общие сведения о [стандартных задачах](#manage-your-sql-vm).

> [!NOTE]
> Если вы уже знакомы с решением SQL Server и просто хотите узнать, как развертывать виртуальную машину SQL Server, см. статью [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).
> 
> 

## <a name="overview"></a>Обзор
Виртуальные машины Azure предоставляют разработчикам или администраторам базы данных возможность перенести локальные рабочие нагрузки и приложения SQL Server в облако. В следующем видео представлен технический обзор использования SQL Server на виртуальных машинах Azure.

> [!VIDEO https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

В этом видео рассматриваются следующие вопросы:

| Время | Область |
| --- | --- |
| 00:21 |Что такое виртуальные машины Azure? |
| 01:45 |Безопасность |
| 02:50 |Соединение |
| 03:30 |Надежность и быстродействие хранилища |
| 05:20 |Размеры виртуальной машины |
| 05:54 |Высокая доступность и соглашение об уровне обслуживания |
| 07:30 |Поддержка конфигурации |
| 08:00 |Мониторинг |
| 08:32 |Демонстрация: создание виртуальной машины SQL Server 2016 |

> [!NOTE]
> Это видео посвящено SQL Server 2016, но Azure предоставляет образы виртуальных машин для многих версий SQL Server, в том числе SQL Server 2008, 2012, 2014 и 2016. 
> 
> 

## <a name="scenarios"></a>Сценарии
Существует множество причин для размещения данных в Azure. Если перенести приложение в Azure, вы также сможете переносить содержащиеся в нем данные, что повышает его производительность. Но это еще не все преимущества. Вы автоматически получаете доступ ко множеству центров обработки данных, что позволяет обеспечить глобальное присутствие и аварийное восстановление. Кроме того, данные надежно защищены и хранятся на протяжении длительного срока.

Выполнение SQL Server на виртуальной машине Azure — один из вариантов хранения реляционных данных в Azure. Это хороший выбор для нескольких сценариев. Например, чтобы настроить виртуальную машину Azure практически как локальный компьютер SQL Server или запустить дополнительные приложения и службы на одном сервере базы данных. Используя ресурсы, приведенные ниже, вы можете продумать в деталях еще больше сценариев и факторов.

* [SQL Server на виртуальных машинах](https://azure.microsoft.com/services/virtual-machines/sql-server/) — обзор лучших сценариев использования SQL Server на виртуальных машинах Azure. 
* [Вы можете выбрать компонент SQL Server в облаке: база данных SQL Azure (PaaS) или SQL Server на виртуальных машинах Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) — детальное сравнение базы данных SQL и SQL Server на виртуальной машине.

## <a name="create-a-new-sql-vm"></a>Создание виртуальной машины SQL
В следующих разделах приведены прямые ссылки на портал Azure для получения образов виртуальной машины SQL Server из коллекции. В зависимости от выбранного образа вы можете оплачивать затраты на лицензирование SQL Server на поминутной основе или использовать собственную лицензию (BYOL).

Пошаговое руководство см. в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md). Просмотрите также статью [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md), в которой объясняется, как выбрать соответствующий размер машины и настроить другие функции, доступные при подготовке.

## <a name="option-1-create-a-sql-vm-with-perminute-licensing"></a>Вариант 1. Создание виртуальной машины SQL с лицензированием по поминутному тарифу
Следующая таблица содержит матрицу доступных образов SQL Server в коллекции виртуальных машин. Выберите необходимые версию, выпуск и операционную систему и щелкните соответствующую ссылку, чтобы начать создание виртуальной машины SQL.

| Версия | Операционная система | Выпуск |
| --- | --- | --- |
| **SQL 2016** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2) |
| **SQL 2014 с пакетом обновления 1 (SP1)** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2) |
| **SQL 2014** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2) |
| **SQL 2012 с пакетом обновления 3 (SP3)** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL 2012 с пакетом обновления 2 (SP2)** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2) |
| **SQL 2012 с пакетом обновления 2 (SP2)** |Windows Server 2012 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012) |
| **SQL 2008 R2 с пакетом обновления 3 (SP3)** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |
| **SQL 2008 R2 с пакетом обновления 3 (SP3)** |Windows Server 2012 |[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012) |

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Вариант 2. Создание виртуальной машины SQL с имеющейся лицензией
Вы также можете использовать собственную лицензию (BYOL). В этом сценарии вы платите только за виртуальную машину и не несете расходы на лицензирование SQL Server. Если вы хотите использовать собственную лицензию, воспользуйтесь приведенной ниже матрицей версий, выпусков SQL Server и операционных систем. На портале имена таких образов начинаются с префикса **{BYOL}**.

| Версия | операционная система | Выпуск |
| --- | --- | --- |
| **SQL Server 2016** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2) |
| **SQL Server 2014 SP1** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

> [!IMPORTANT]
> Чтобы использовать образы виртуальных машин BYOL, требуется соглашение Enterprise и возможность [перемещения лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility/). Вам также потребуется действительная лицензия для версии или выпуска SQL Server, которые необходимо использовать. [Соответствующие сведения, касающиеся BYOL, необходимо предоставить корпорации Майкрософт](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) в течение **10** дней после подготовки виртуальной машины.
> 
> 

## <a name="manage-your-sql-vm"></a>Управление виртуальной машиной SQL
После подготовки виртуальной машины SQL Server вы можете выполнить несколько дополнительных задач по управлению. Во многих аспектах настройка и управление SQL Server ничем не отличаются от аналогичных процедур в локальном экземпляре SQL Server. Однако некоторые задачи являются характерными для Azure. В следующих разделах рассматриваются некоторые из этих задач и приведены ссылки на дополнительные сведения.

### <a name="connect-to-the-vm"></a>Подключение к виртуальной машине
Один из основных этапов управления заключается в подключении к виртуальной машине SQL Server с помощью таких средств, как SQL Server Management Studio (SSMS). Дополнительные сведения о том, как подключиться к новой виртуальной машине SQL Server, см. в статье [Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов)](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Перенос данных
Если у вас есть база данных, вам необходимо будет переместить ее в новую виртуальную машину SQL. Список вариантов и руководство по миграции баз данных см. в статье [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Настройка высокой доступности
Если требуется высокий уровень доступности, вам помогут группы доступности SQL Server. Это подразумевает использование нескольких виртуальных машин Azure в виртуальной сети. На портале Azure есть шаблон, который настраивает эту конфигурацию. Дополнительные сведения см. в статье [Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Если вы хотите вручную настроить группу доступности и связанный прослушиватель, см. сведения в статье [Ручная настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Дополнительные рекомендации по обеспечению высокого уровня доступности см. в статье [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Резервное копирование данных
Виртуальные машины Azure поддерживают [автоматическую архивацию](virtual-machines-windows-sql-automated-backup.md) — функцию, которая регулярно создает резервные копии базы данных в хранилище BLOB-объектов. Это также можно выполнять вручную. Дополнительные сведения см. в статье [Использование службы хранилища Azure для архивации и восстановления SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Обзор параметров резервного копирования и восстановления см. в статье [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Автоматизация обновлений
Виртуальные машины Azure также поддерживают [автоматическую установку исправлений](virtual-machines-windows-sql-automated-patching.md) — функцию, позволяющую планировать график автоматической установки важных обновлений для Windows и SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Программа улучшения качества программного обеспечения (CEIP)
Программа по улучшению качества программного обеспечения (CEIP) включена по умолчанию. Она периодически отправляет отчеты в Майкрософт, чтобы улучшить качество SQL Server. Если вы не хотите отключить CEIP после подготовки, то дополнительные задания управления не требуются. Вы можете настроить или отключить программу CEIP, подключившись к виртуальной машине с помощью удаленного рабочего стола. Затем запустите служебную программу **Служба отчетов об ошибках и использовании SQL Server** . Следуйте инструкциям в окнах этой программы, чтобы отключить создание отчетов. 

Дополнительные сведения см. в разделе "Программа улучшения качества программного обеспечения (CEIP)" статьи [Принятие условий лицензионного соглашения](https://msdn.microsoft.com/library/ms143343.aspx). 

## <a name="next-steps"></a>Дальнейшие действия
[Ознакомьтесь со схемой обучения](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) по использованию SQL Server в виртуальных машинах Azure.

У вас остались вопросы? Сначала ознакомьтесь со статьей [Часто задаваемые вопросы об SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-faq.md). Чтобы получить поддержку от корпорации Майкрософт и сообщества, вы также можете оставить комментарий под любой статьей о виртуальных машинах SQL.




<!--HONumber=Nov16_HO2-->


