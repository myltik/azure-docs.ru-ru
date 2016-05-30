<properties
	pageTitle="Обзор SQL Server на виртуальных машинах | Microsoft Azure"
	description="Начало работы с базами данных SQL Server в облаке на виртуальных машинах Azure. Модель инфраструктуры как услуги (IaaS) позволяет запускать рабочие нагрузки SQL Server в Azure."
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
	ms.date="05/18/2016"
	ms.author="jroth"/>

# Общие сведения об SQL Server на виртуальных машинах Azure

[SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) позволяет разместить в облаке базы данных SQL Server. Например, вы можете перенести локальную базу данных на виртуальную машину Azure, на которой установлены Windows Server 2012 R2 и SQL Server 2014 Enterprise edition. Но существует и множество других сценариев, например конфигурация с несколькими компьютерами, которая обеспечит высокий уровень доступности, или гибридные архитектуры с подключением к локальным сетям.

Подробный обзор см. в этом видео:

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Предложения для SQL

Выполнение SQL Server на виртуальной машине Azure — один из вариантов хранения реляционных данных в Azure. Полный список вариантов представлен в следующей таблице.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Предложение для SQL | Описание |
|---:|---|---|
|![SQL Server на виртуальных машинах Azure](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[SQL Server на виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Запустите SQL Server на виртуальных машинах Azure. Вы сможете напрямую управлять виртуальной машиной и запустить базу данных на коммерческой версии SQL Server. |
|![База данных SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[База данных SQL](https://azure.microsoft.com/services/sql-database/)|Используйте службу базы данных SQL, чтобы обращаться к базе данных и масштабировать ее, не отвлекаясь на управление базовой инфраструктурой.|
|![Хранилище данных SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[Хранилище данных SQL](https://azure.microsoft.com/ru-RU/services/sql-data-warehouse/)|Используйте хранилище данных SQL Azure для обработки больших объемов реляционных и нереляционных данных. Масштабируемые возможности хранения данных предоставляются как служба.|
|![База данных SQL Server Stretch](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[База данных SQL Server Stretch](https://azure.microsoft.com/ru-RU/services/sql-server-stretch-database/)|Динамически объединяйте локальные данные транзакций с базой данных Microsoft SQL Server 2016, размещенной в Azure.|

>[AZURE.NOTE] Подробное сравнение см. в статье [Вы можете выбрать компонент SQL Server в облаке: база данных SQL Azure (PaaS) или SQL Server на виртуальных машинах Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

## Развертывание виртуальной машины SQL Server

Для создания виртуальной машины SQL Server в Azure сначала необходимо получить подписку на платформу Azure. Приобрести подписку Azure можно в разделе [Варианты приобретения](https://azure.microsoft.com/pricing/purchase-options/). Чтобы попробовать ее бесплатно, зайдите на страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

После получения подписки самый простой способ развернуть виртуальную машину SQL Server в Azure — [подготовить образ из коллекции виртуальных машин SQL Server в Azure](virtual-machines-windows-portal-sql-server-provision.md). Эти образы содержат лицензии на использование SQL Server, стоимость которых включена в цену виртуальной машины.

Имеются две модели для создания виртуальных машин Azure и управления ими: классическая и диспетчер ресурсов. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Подробнее об этих моделях можно узнать в статье [Общие сведения о развертывании диспетчера ресурсов и классическом развертывании](../resource-manager-deployment-model.md). В каждой статье будет явно указана целевая модель, за исключением таких статей, которые применимы и для классической модели, и для Resource Manager (например, эта статья).

## Выберите образ виртуальной машины SQL
Следующая таблица содержит матрицу доступных образов SQL Server в коллекции виртуальных машин. Щелкните любую ссылку в таблице, чтобы создать виртуальную машину для указанной версии, выпуска и операционной системы.

|Версия SQL Server|операционная система|Выпуск SQL Server|
|---|---|---|
|**SQL Server 2016 RC**|Windows Server 2012 R2|[Оценка](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rc3evaluationwindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] Программа по улучшению качества программного обеспечения (CEIP) включена по умолчанию. При необходимости вы можете настроить или отключить CEIP после подготовки виртуальной машины. Подключитесь к виртуальной машине с удаленного рабочего стола и запустите **службу отчетов об ошибках и использовании SQL Server**.

Наряду с использованием этих предварительно настроенных образов можно [создать виртуальную машину Azure](virtual-machines-windows-hero-tutorial.md) без предустановленного SQL Server. Можно установить любой экземпляр SQL Server с действующей лицензией. Перенесите лицензию в Azure для запуска SQL Server в виртуальной машине Azure, воспользовавшись инструкциями из статьи [Перемещение лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility/). В этом случае вы оплачиваете только [затраты](https://azure.microsoft.com/pricing/details/virtual-machines/) на вычислительные ресурсы и ресурсы хранения Azure, связанные с виртуальной машиной.

Чтобы определить наиболее подходящие параметры виртуальных машин для образа SQL Server, см. сведения в статье [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md). Для рабочих нагрузок в рабочей среде **DS3** — минимальный рекомендуемый размер виртуальной машины для SQL Server Enterprise, а **DS2** — для выпуска Standard.

## Перенос данных

После запуска виртуальной машины SQL Server может потребоваться перенести существующие базы данных на нее. Список вариантов и руководство по миграции баз данных см. в статье [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md).

## высокую доступность;

Если требуется высокий уровень доступности, вам помогут группы доступности SQL Server. Это подразумевает использование нескольких виртуальных машин Azure в виртуальной сети. На портале Azure есть шаблон, который настраивает эту конфигурацию. Подробнее см. в статье [Настройка группы доступности AlwaysOn на виртуальных машинах диспетчера ресурсов Azure с помощью графического пользовательского интерфейса ](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

Если вы хотите вручную настроить группу доступности и связанный прослушиватель, см. статью [Настройка групп доступности AlwaysOn в виртуальной машине Azure (графический пользовательский интерфейс)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Дополнительные рекомендации по обеспечению высокого уровня доступности см. в разделе [Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md).

## Архивация и восстановление
Для локальных баз данных Azure может действовать как дополнительный центр обработки данных для хранения файлов резервных копий SQL Server. Обзор параметров резервного копирования и восстановления см. в статье [Резервное копирование и восстановление для SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-backup-recovery.md).

[Резервное копирование SQL Server на URL-адрес](https://msdn.microsoft.com/library/dn435916.aspx) сохраняет файлы резервных копий Azure в хранилище больших двоичных объектов. [Управляемое резервное копирование SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) позволяет планировать резервное копирование и хранение данных в Azure. Эти службы могут использоваться с локальными экземплярами SQL Server или SQL Server на виртуальных машинах Azure. Виртуальные машины Azure могут также использовать преимущества [автоматического резервного копирования](virtual-machines-windows-classic-sql-automated-backup.md) и [автоматического обновления путем частичной замены](virtual-machines-windows-classic-sql-automated-patching.md) для SQL Server.

## Дальнейшие действия

Во-первых, [создайте собственную виртуальную машину SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

Затем просмотрите [рекомендации по повышению производительности](virtual-machines-windows-sql-performance.md) и [методы переноса](virtual-machines-windows-migrate-sql.md), которые помогут перенести рабочие нагрузки SQL Server на виртуальные машины Azure.

Если у вас останутся вопросы о работе SQL Server на виртуальных машинах Azure, изучите статью [Часто задаваемые вопросы об SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-faq.md). Чтобы получить поддержку от корпорации Майкрософт и сообщества, вы можете оставить комментарий под любой статьей о виртуальных машинах SQL.

<!---HONumber=AcomDC_0518_2016-->