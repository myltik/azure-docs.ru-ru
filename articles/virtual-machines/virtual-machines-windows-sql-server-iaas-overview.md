<properties
	pageTitle="Обзор SQL Server на виртуальных машинах Azure | Microsoft Azure"
	description="Из этой статьи вы узнаете, как запускать полные выпуски SQL Server на виртуальных машинах Azure. Прямые ссылки на все образы виртуальных машин SQL Server и соответствующие статьи."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/21/2016"
	ms.author="jroth"/>

# Приступая к работе с SQL Server в виртуальных машинах Azure

В этой статье описано, как использовать решение SQL Server на виртуальных машинах Azure, а также проведены [ссылки на образы портала](#option-1-deploy-a-sql-vm-per-minute-licensing) и общие сведения о [стандартных задачах](#manage-your-sql-vm).

>[AZURE.NOTE] Если вы уже знакомы с решением SQL Server и просто хотите узнать, как развертывать виртуальную машину SQL Server, см. статью [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

## Обзор
Возможно, вы администратор базы данных, которому необходимо переместить локальные рабочие нагрузки SQL Server в облако, или разработчик, рассматривающий использование возможностей реляционной базы данных SQL Server для приложения Azure. Какие же преимущества предоставляет выполнение рабочих нагрузок SQL Server в виртуальных машинах Azure? В приведенном ниже видео рассматриваются преимущества и представлен технический обзор.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Оценка преимуществ

Прежде чем начать, следует оценить преимущества использования SQL Server в виртуальных машинах Azure.

При перемещении других рабочих нагрузок в Azure (например, корпоративных приложений) целесообразно также переместить все зависимые базы данных SQL Server в Azure, чтобы повысить производительность. Однако размещение нагрузок SQL Server в виртуальных машинах Azure предоставляет и другие преимущества. Например, вы автоматически получаете доступ ко множеству центров обработки данных, что позволяет обеспечить глобальное присутствие и аварийное восстановление. Полный список сценариев и преимуществ можно найти на странице [SQL Server на виртуальных машинах](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] При оценке использования SQL Server в виртуальных машинах Azure следует также рассмотреть другие варианты хранилищ и служб SQL Azure, в том числе [базу данных SQL](../sql-database/sql-database-technical-overview.md), [хранилище данных SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) и [базу данных SQL Server Stretch](../sql -server-stretch-database/sql-server-stretch-database-overview.md). Подробное сравнение см. в статье [Вы можете выбрать компонент SQL Server в облаке: база данных SQL Azure (PaaS) или SQL Server на виртуальных машинах Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

Решив выполнять рабочие нагрузки SQL Server в виртуальных машинах Azure, следует определиться, будете ли вы использовать образ виртуальной машины, включающий затраты на лицензирование SQL Server. Можно также использовать собственную лицензию (BYOL) и платить только за виртуальную машину. Эти варианты описаны в следующих двух разделах.

## Создание виртуальной машины SQL
В следующих разделах приведены прямые ссылки на портал Azure для получения образов виртуальной машины SQL Server из коллекции. В зависимости от выбранного образа вы можете оплачивать затраты на лицензирование SQL Server на поминутной основе или использовать собственную лицензию (BYOL).

Пошаговые инструкции см. в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md). Просмотрите также статью [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md), в которой объясняется, как выбрать соответствующий размер машины и настроить другие функции, доступные при подготовке.

## Вариант 1. Создание виртуальной машины SQL с лицензированием по поминутному тарифу
Следующая таблица содержит матрицу доступных образов SQL Server в коллекции виртуальных машин. Выберите необходимые версию, выпуск и операционную систему и щелкните соответствующую ссылку, чтобы начать создание виртуальной машины SQL.

|Версия|Операционная система|Выпуск|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 с пакетом обновления 1 (SP1)**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 с пакетом обновления 3 (SP3)**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 с пакетом обновления 2 (SP2)**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 с пакетом обновления 2 (SP2)**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 с пакетом обновления 3 (SP3)**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 с пакетом обновления 3 (SP3)**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Вариант 2. Создание виртуальной машины SQL с имеющейся лицензией
Вы также можете использовать собственную лицензию (BYOL). В этом сценарии вы платите только за виртуальную машину и не несете расходы на лицензирование SQL Server. Если вы хотите использовать собственную лицензию, воспользуйтесь приведенной ниже матрицей версий, выпусков SQL Server и операционных систем. На портале имена таких образов начинаются с префикса **{BYOL}**.

|Версия|операционная система|Выпуск|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Чтобы использовать образы виртуальных машин BYOL, требуется соглашение Enterprise и возможность [перемещения лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility/). Вам также потребуется действительная лицензия для версии или выпуска SQL Server, которые необходимо использовать. [Соответствующие сведения, касающиеся BYOL, необходимо предоставить корпорации Майкрософт](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) в течение **10** дней после подготовки виртуальной машины.

## Управление виртуальной машиной SQL
После подготовки виртуальной машины SQL Server вы можете выполнить несколько дополнительных задач по управлению. Во многих аспектах настройка и управление SQL Server ничем не отличаются от аналогичных процедур в локальном экземпляре SQL Server. Однако некоторые задачи являются характерными для Azure. В следующих разделах рассматриваются некоторые из этих задач и приведены ссылки на дополнительные сведения.

### Перенос данных

Если у вас есть база данных, вам необходимо будет переместить ее в новую виртуальную машину SQL. Список вариантов и руководство по миграции баз данных см. в статье [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md).

### Настройка высокой доступности

Если требуется высокий уровень доступности, вам помогут группы доступности SQL Server. Это подразумевает использование нескольких виртуальных машин Azure в виртуальной сети. На портале Azure есть шаблон, который настраивает эту конфигурацию. Дополнительные сведения см. в статье [Автоматическая настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Если вы хотите вручную настроить группу доступности и связанный прослушиватель, см. сведения в статье [Ручная настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Дополнительные рекомендации по обеспечению высокого уровня доступности см. в разделе [Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md).

### Резервное копирование данных
Виртуальные машины Azure поддерживают [автоматическую архивацию](virtual-machines-windows-sql-automated-backup.md) — функцию, которая регулярно создает резервные копии базы данных в хранилище BLOB-объектов. Это также можно выполнять вручную. Дополнительные сведения см. в статье [Использование службы хранилища Azure для архивации и восстановления SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Обзор параметров резервного копирования и восстановления см. в статье [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

### Автоматизация обновлений
Виртуальные машины Azure также поддерживают [автоматическую установку исправлений](virtual-machines-windows-sql-automated-patching.md) — функцию, позволяющую планировать график автоматической установки важных обновлений для Windows и SQL Server.

### Программа улучшения качества программного обеспечения (CEIP)
Программа по улучшению качества программного обеспечения (CEIP) включена по умолчанию. Это рассматривается как задача управления, только если вы хотите отключить эту программу после подготовки. Вы можете настроить или отключить программу CEIP, подключившись к виртуальной машине с помощью удаленного рабочего стола. Затем запустите служебную программу **Служба отчетов об ошибках и использовании SQL Server**. Следуйте инструкциям в окнах этой программы, чтобы отключить создание отчетов.

## Дальнейшие действия
[Ознакомьтесь со схемой обучения](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) по использованию SQL Server в виртуальных машинах Azure.

У вас остались вопросы? Сначала ознакомьтесь со статьей [Часто задаваемые вопросы об SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-faq.md). Чтобы получить поддержку от корпорации Майкрософт и сообщества, вы также можете оставить комментарий под любой статьей о виртуальных машинах SQL.

<!---HONumber=AcomDC_0921_2016-->