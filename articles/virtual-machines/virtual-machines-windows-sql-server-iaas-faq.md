<properties
	pageTitle="Часто задаваемые вопросы об SQL Server в виртуальных машинах Azure | Microsoft Azure"
	description="В этой статье содержатся ответы на часто задаваемые вопросы о запуске SQL Server в виртуальных машинах Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/15/2016"
	ms.author="jroth"/>

# Часто задаваемые вопросы об SQL Server в виртуальных машинах Azure

В этой статье содержатся ответы на некоторые наиболее распространенные вопросы о запуске [SQL Server в виртуальных машинах Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

## Часто задаваемые вопросы

1. **Как создать виртуальную машину Azure с SQL Server?**

	Это можно сделать двумя способами. Самый простой способ — создать виртуальную машину с SQL Server. Учебное руководство по регистрации в Azure и созданию виртуальной машины SQL на портале см. в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md). Вы также можете вручную установить SQL Server в виртуальной машине и повторно использовать локальную лицензию благодаря возможности [перемещения лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **В чем разница между виртуальными машинами SQL и службой "База данных SQL"?**

	По существу выполнение SQL Server в виртуальной машине Azure не отличается от выполнения SQL Server в удаленном центре данных. В свою очередь, служба [База данных SQL](../sql-database/sql-database-technical-overview.md) предлагается на основе модели "база данных как услуга". При использовании Базы данных SQL у вас нет доступа к компьютерам, на которых размещаются ваши базы данных. Полное сравнение см. в статье [Вы можете выбрать компонент SQL Server в облаке: база данных SQL Azure (PaaS) или SQL Server на виртуальных машинах Azure (IaaS)](../sql-database/media/data-management-azure-sql-database-and-sql-server-iaas.md).

1. **Как перенести локальную базу данных SQL Server в облако?**

	Сначала создайте виртуальную машину Azure с экземпляром SQL Server. Затем перенесите локальные базы данных в этот экземпляр. Стратегии переноса данных см. в статье [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md).

2. **Можно ли изменить установленные компоненты или установить второй экземпляр SQL Server в одной виртуальной машине?**

	Да. Установочный носитель SQL Server находится в папке на диске **C**. Запустите файл **Setup.exe** из этого места, чтобы добавить новые экземпляры SQL Server или изменить другие компоненты SQL Server, установленные в машине.

3. **Как выполнить обновление до новой версии или выпуска SQL Server в виртуальной машине Azure?**

	В настоящее время выполнить обновление на месте для SQL Server в виртуальной машине Azure невозможно. Создайте виртуальную машину Azure с нужной версией или выпуском SQL Server, а затем перенесите базы данных на новый сервер с помощью стандартных [методов переноса данных](virtual-machines-windows-migrate-sql.md).

4. **Как установить лицензированную копию SQL Server в виртуальной машине Azure?**

	Скопируйте установочный носитель SQL Server в виртуальную машину Windows Server, а затем установите SQL Server в виртуальной машине. В соответствии с требованиями лицензирования у вас должно быть преимущество [Перемещение лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **Необходимо ли оплачивать использование SQL в виртуальной машине, если SQL применяется только в целях резервирования и отработки отказа?**

	Если вы создаете виртуальную машину SQL посредством коллекции, то у вас должна быть отдельная лицензия на резервную виртуальную машину SQL и в ее отношении действуют стандартные расценки. Если вы устанавливаете SQL Server в виртуальной машине вручную посредством [перемещения лицензий](https://azure.microsoft.com/pricing/license-mobility/), вам доступен один бесплатный пассивный экземпляр SQL для отработки отказа. Ознакомьтесь с ограничениями и требованиями.

6. **Как обновления и пакеты обновления применяются к виртуальной машине SQL Server?**

	Виртуальные машины позволяют контролировать сервер, в том числе время и способ применения обновлений. Что касается операционной системы, вы можете вручную применять обновления для Windows или включить службу расписаний под названием [Автоматическое исправление](virtual-machines-windows-classic-sql-automated-patching.md). Эта служба устанавливает все обновления, помеченные как важные, включая обновления SQL Server из этой категории. Другие необязательные обновления для SQL Server необходимо устанавливать вручную.

7. **Можно ли настраивать конфигурации, которых нет в коллекции виртуальных машин (например Windows 2008 R2 + SQL Server 2012)?**

	Нет. Вы можете выбрать только один из представленных в коллекции образов виртуальных машин с SQL Server.

9. **Как установить SQL Server Data Tools в виртуальной машине Azure?**

	Скачайте и установите SQL Server Data Tools на странице [Microsoft SQL Server Data Tools ― Business Intelligence для Visual Studio 2013](https://www.microsoft.com/ru-RU/download/details.aspx?id=42313).

## Ресурсы

Чтобы получить общие сведения об SQL Server в виртуальных машинах Azure, просмотрите видеоролик [Виртуальные машины Azure — лучшая платформа для SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). Хорошее введение в тему также можно найти в статье [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Другие ресурсы:

- [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)
- [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md)
- [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

<!---HONumber=AcomDC_0427_2016-->