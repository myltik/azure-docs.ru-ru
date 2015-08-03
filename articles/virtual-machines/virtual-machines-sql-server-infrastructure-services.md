<properties 
	pageTitle="SQL Server на виртуальных машинах Azure" 
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
	ms.date="07/17/2015"
	ms.author="jroth"/>

# SQL Server на виртуальных машинах Azure

## Обзор
Вы можете разместить [SQL Server на виртуальных машинах Azure][sqlvmlanding] в различных конфигурациях, от одного сервера базы данных до мультикомпьютерных конфигураций с использованием групп доступности AlwaysOn и виртуальной сети Azure.

> [AZURE.NOTE]Выполнение SQL Server на виртуальной машине Azure — один из вариантов хранения реляционных данных в Azure. Можно также использовать службу базы данных SQL Azure. Дополнительную информацию см. в разделе [Общее представление о базе данных SQL Azure и SQL Server в виртуальных машинах Azure][sqldbcompared].
 
## Развертывание экземпляра SQL Server на одной виртуальной машине
После того, как вы [создали виртуальную машину Azure с помощью портала Azure][createvmportal] или автоматически, на нее можно установить любой экземпляр SQL Server, для которого у вас имеется лицензия. Тем не менее, необходимо выполнить дополнительные шаги, чтобы [настроить подключение][setupconnectivity] между компьютером SQL Server и другими клиентскими компьютерами.
 
Вы можете также установить один из многих различных образов виртуальных машин SQL Server из коллекции. Эти образы содержат лицензии на использование SQL Server, стоимость которых включена в цену виртуальной машины. Дополнительную информацию и пошаговое описание подключения см. в разделе [Подготовка виртуальной машины SQL Server в Azure][provisionsqlvm].

После запуска виртуальной машины SQL Server может потребоваться перенести существующие базы данных на нее. Дополнительную информацию о миграции баз данных см. в разделе [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md).

## Развертывание высокодоступной конфигурации с несколькими виртуальными машинами
Можно достичь высокого уровня доступности SQL Server с помощью группы доступности AlwaysOn SQL Server. Это подразумевает использование нескольких виртуальных машин Azure в виртуальной сети. На портале предварительной версии Azure есть шаблон, который настраивает эту конфигурацию. Дополнительную информацию см. в разделе [Предложения AlwaysOn SQL Server в коллекции портала Microsoft Azure][sqlalwaysonportal]. Или можно [вручную настроить группу доступности AlwaysOn][sqlalwaysonmanual]. Дополнительные рекомендации по обеспечению высокого уровня доступности см. в разделе [Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах Azure][sqlhadr].

## Выполнение бизнес-аналитики, хранение данных и рабочие нагрузки OLTP в Azure   
Вы можете выполнять общие рабочие нагрузки SQL Server на виртуальных машинах Azure. Для SQL Server есть несколько оптимизированных образов виртуальных машин, которые доступны в коллекции. Это образы для [бизнес-аналитики][sqlbi], [хранения данных][sqldw] и [OLTP][sqloltp].

## Перенос данных
После запуска виртуальной машины SQL Server может потребоваться перенести существующие базы данных на нее. Существует несколько методов, но для большинства сценариев хорошо подходит мастер развертывания в SQL Server Management Studio. Учебник по мастеру и обсуждение сценариев см. в разделе [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md).

## Архивация и восстановление
Для локальных баз данных Azure может действовать как дополнительный центр обработки данных для хранения файлов резервных копий SQL Server. [Резервное копирование SQL Server на URL-адрес][backupurl] сохраняет файлы резервных копий Azure в хранилище больших двоичных объектов. [Управляемое резервное копирование SQL Server][managedbackup] позволяет планировать резервное копирование и хранение данных в Azure. Эти службы могут использоваться с локальными экземплярами SQL Server или SQL Server на виртуальных машинах Azure. Виртуальные машины Azure могут также использовать преимущества [автоматического резервного копирования][autobackup] и [автоматического обновления путем частичной замены][autopatching] для SQL Server. Дополнительную информацию см. в разделе [Задачи управления SQL Server на виртуальных машинах Azure][managementtasks].

## Ресурсы:
[SQL Server на виртуальных машинах Azure][sqlmsdnlanding]

[Подготовка виртуальной машины SQL Server в Azure][provisionsqlvm]

[Приступая к работе с SQL Server на виртуальных машинах Azure][sqlvmgetstarted]

[Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md)

[Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure][sqlperf]

[Вопросы безопасности SQL Server в виртуальных машинах Azure][sqlsecurity]

  [sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
  [sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
  [createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
  [setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
  [provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
  [sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
  [sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
  [sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
  [sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
  [sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
  [sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
  [migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
  [backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
  [managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
  [autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
  [autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
  [managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
  [sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
  [sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
  [sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
  [sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
  [technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx

<!---HONumber=July15_HO4-->