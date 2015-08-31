<properties 
	pageTitle="Обзор. SQL Server на виртуальных машинах Azure" 
	description="В этой статье приводятся общие сведения о размещении SQL Server на виртуальных машинах Azure (IaaS). Также включены ссылки на более подробное содержимое." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Обзор. SQL Server на виртуальных машинах Azure

## Обзор
Вы можете разместить [SQL Server на виртуальных машинах Azure](http://azure.microsoft.com/services/virtual-machines/sql-server/) в различных конфигурациях, от одного сервера базы данных до мультикомпьютерных конфигураций с использованием групп доступности AlwaysOn и виртуальной сети Azure. В этом разделе рассматриваются некоторые ресурсы, которые целесообразно использовать при первом запуске SQL Server на виртуальной машине Azure.

>[AZURE.NOTE]Выполнение SQL Server на виртуальной машине Azure — один из вариантов хранения реляционных данных в Azure. Можно также использовать службу базы данных SQL Azure. Дополнительную информацию см. в разделе [Общее представление о базе данных SQL Azure и SQL Server в виртуальных машинах Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).
 
## Развертывание экземпляра SQL Server на одной виртуальной машине

Самый простой способ развернуть виртуальную машину SQL Server в Azure — [подготовить образ коллекции машин SQL Server на портале управления Azure](virtual-machines-provision-sql-server.md). Эти образы содержат лицензии на использование SQL Server, стоимость которых включена в цену виртуальной машины.

Однако вы также можете [создать виртуальную машину Azure](virtual-machines-windows-tutorial.md) без предварительной установки SQL Server. Можно установить любой экземпляр SQL Server с действующей лицензией.

На этих ранних этапах подготовки и настройки выполняются следующие общие задачи:

- [анализ рекомендаций по оптимизации производительности SQL Server в виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx);
- [анализ рекомендаций по безопасности SQL Server в виртуальных машинах Azure](https://msdn.microsoft.com/library/azure/dn133147.aspx);
- [настройка соединения](virtual-machines-sql-server-connectivity.md).

## Развертывание высокодоступной конфигурации с несколькими виртуальными машинами

Можно достичь высокого уровня доступности SQL Server с помощью группы доступности AlwaysOn SQL Server. Это подразумевает использование нескольких виртуальных машин Azure в виртуальной сети. На портале предварительной версии Azure есть шаблон, который настраивает эту конфигурацию. Дополнительную информацию см. в разделе [Предложения AlwaysOn SQL Server в коллекции портала Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Если вы хотите вручную настроить группу доступности и связанный прослушиватель, см. следующие статьи.

- [Настройка групп доступности AlwaysOn в Azure (графический пользовательский интерфейс)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Настройка прослушивателя внутренней подсистемы балансировки нагрузки для группы доступности AlwaysOn в Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Расширение локальных групп доступности AlwaysOn в Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Дополнительные рекомендации по обеспечению высокого уровня доступности см. в разделе [Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Выполнение бизнес-аналитики, хранение данных и рабочие нагрузки OLTP в Azure   
Вы можете выполнять общие рабочие нагрузки SQL Server на виртуальных машинах Azure. Для SQL Server есть несколько оптимизированных образов виртуальных машин, которые доступны в коллекции. К ним относятся образы для следующих компонентов:

- [бизнес-аналитика](https://msdn.microsoft.com/library/azure/jj992719.aspx);
- [хранилища данных](https://msdn.microsoft.com/library/azure/dn387396.aspx);
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx).

## Перенос данных

После запуска виртуальной машины SQL Server может потребоваться перенести существующие базы данных на нее. Существует несколько методов, но для большинства сценариев хорошо подходит мастер развертывания в SQL Server Management Studio. Учебник по мастеру и обсуждение сценариев см. в статье [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md).

## Архивация и восстановление
Для локальных баз данных Azure может действовать как дополнительный центр обработки данных для хранения файлов резервных копий SQL Server. Обзор параметров резервного копирования и восстановления см. в статье [Резервное копирование и восстановление SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-backup-and-restore.md).

[Резервное копирование SQL Server на URL-адрес](https://msdn.microsoft.com/library/dn435916.aspx) сохраняет файлы резервных копий Azure в хранилище больших двоичных объектов. [Управляемое резервное копирование SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) позволяет планировать резервное копирование и хранение данных в Azure. Эти службы могут использоваться с локальными экземплярами SQL Server или SQL Server на виртуальных машинах Azure. Виртуальные машины Azure могут также использовать преимущества [автоматического резервного копирования](virtual-machines-sql-server-automated-backup.md) и [автоматического обновления путем частичной замены](virtual-machines-sql-server-automated-patching.md) для SQL Server.

<!---HONumber=August15_HO8-->