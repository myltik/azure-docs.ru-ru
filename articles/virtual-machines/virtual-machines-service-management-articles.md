<properties
	pageTitle="Статьи по виртуальным машинам в управлении службами | Microsoft Azure"
	description="Список статей, которые помогут создавать виртуальные машины в управлении службами Azure и управлять ими."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-multiple"
	ms.workload="infrastructure-services"
	ms.date="06/30/2015"
	ms.author="danlep"/>

# Статьи по виртуальным машинам в управлении службами
Это список статей для работы с виртуальными машинами в управлении службами Azure. Для новых развертываний мы советуем использовать виртуальные машины на основе диспетчера ресурсов Azure и другие ресурсы. Объяснение преимуществ диспетчера ресурсов см. в статье [Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md).

## Начало работы

[Создание виртуальной машины под управлением Windows на портале управления Azure](virtual-machines-windows-tutorial-classic-portal.md)

[Как создать настраиваемую виртуальную машину под управлением Windows](virtual-machines-windows-create-custom.md)

[Как создать настраиваемую виртуальную машину под управлением Linux](virtual-machines-linux-create-custom.md)

[Учебник. Создание виртуальной сети только для облака в Azure.](create-virtual-network.md)

## Автоматизация

[Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)

[Использование Azure PowerShell для создания и предварительной настройки виртуальных машин под управлением Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

[Создание виртуальной машины Windows и управление ею с помощью PowerShell и управления службами Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Как создать виртуальную машину с помощью интерфейса командной строки Azure (Azure CLI)](virtual-machines-xplat-getting-started.md)

[Развертывание нескольких виртуальных машин с помощью интерфейса командной строки Azure](virtual-machines-create-multi-vm-deployment-xplat-cli.md)

## План

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[О параметрах конфигурации виртуальной машины](https://msdn.microsoft.com/library/azure/dn763935.aspx)

[Нужна ли мне виртуальная сеть?](https://msdn.microsoft.com/library/azure/jj156007.aspx)

## Создание

[Создание и передача виртуального жесткого диска Windows Server в Azure](virtual-machines-create-upload-vhd-windows-server.md)

[Создание и загрузка виртуального жесткого диска, содержащего операционную систему Linux.](virtual-machines-linux-create-upload-vhd.md)

[Как записать виртуальную машину Windows, чтобы использовать в качестве шаблона](virtual-machines-capture-image-windows-server.md)


[Как записать образ виртуальной машины Linux для использования в качестве шаблона](virtual-machines-linux-capture-image.md)

[Как записать образ виртуальной машины под управлением Linux с помощью CLI для дальнейшего использования в качестве шаблона](virtual-machines-vm-capture-image-cli.md)


## Управление

[Как войти в виртуальную машину под управлением Windows Server](virtual-machines-log-on-windows-server.md)

[Как войти в виртуальную машину под управлением Linux](virtual-machines-linux-how-to-log-on.md)

[Подключение диска данных к виртуальной машине Windows](storage-windows-attach-disk.md)

[Как отсоединить диск данных от виртуальной машины Windows](storage-windows-detach-disk.md)

[Подключение диска данных к виртуальной машине Linux](virtual-machines-linux-how-to-attach-disk.md)

[Как отсоединить диск данных от виртуальной машины Linux](virtual-machines-linux-how-to-detach-disk.md)

[Подключение виртуальных машин с помощью виртуальной сети или облачной службы](cloud-services-connect-virtual-machine.md)

[Настройка группы доступности для виртуальных машин](virtual-machines-how-to-configure-availability.md)

[Приступая к настройке внутренней подсистемы балансировки нагрузки](../load-balancer/load-balancer-internal-getstarted.md)

[Приступая к настройке подсистемы балансировки нагрузки, доступной в Интернете](../load-balancer/load-balancer-internet-getstarted.md)

[Настройка конечных точек виртуальной машины](virtual-machines-set-up-endpoints.md)

## Создание рабочих нагрузок

[Active Directory](https://msdn.microsoft.com/library/azure/jj156090.aspx)

[Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)

[Docker](virtual-machines-docker-with-xplat-cli.md)

[Dynamics NAV](https://msdn.microsoft.com/library/azure/dn168977.aspx)

[Пакет HPC](https://msdn.microsoft.com/library/azure/dn518135.aspx)

[Стек LAMP](virtual-machines-linux-install-lamp-stack.md)

[MongoDB в Windows Server](virtual-machines-install-mongodb-windows-server.md)

[MySQL в Windows Server](virtual-machines-mysql-windows-server-2008r2.md)

[Oracle](virtual-machines-oracle-azure-virtual-machines.md)

[SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

[SQL Server](virtual-machines-sql-server-infrastructure-services.md)

## Монитор

[Общая информация об уведомлениях и оповещениях мониторинга в Azure](https://msdn.microsoft.com/library/azure/dn306639.aspx)

[Включение диагностики](../cloud-services/cloud-services-dotnet-diagnostics.md)

[Отладка облачной службы или виртуальной машины в Visual Studio](https://msdn.microsoft.com/library/azure/ff683670.aspx)

[Использование скриптов Windows PowerShell для публикации в среды разработки и тестирования](https://msdn.microsoft.com/library/azure/dn642480.aspx).

## Устранение неполадок

[Устранение неполадок с подключением к удаленному рабочему столу виртуальной машины Windows в службе Azure](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Устранение неполадок с подключением Secure Shell (SSH) к виртуальной машине Azure под управлением Linux](virtual-machines-troubleshoot-ssh-connections.md)

## Справочные материалы

[Командлеты PowerShell для управления службами](https://msdn.microsoft.com/library/azure/dn708504.aspx)

[Справочник по API-интерфейсу управления службами REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[Команды интерфейса командной строки Azure для управления службами](virtual-machines-command-line-tools.md)

<!---HONumber=July15_HO2-->