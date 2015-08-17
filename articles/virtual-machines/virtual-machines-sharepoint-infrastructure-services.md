<properties
	pageTitle="Фермы SharePoint, размещенные в службах инфраструктуры Azure"
	description="Ознакомьтесь с основными статьями, в которых описывается настройка фермы SharePoint 2013 для разработки и тестирования или работы в службах инфраструктуры Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Фермы SharePoint, размещенные в службах инфраструктуры Azure

Настройте свою первую или очередную ферму SharePoint для разработки и тестирования или производства в службах инфраструктуры Microsoft Azure, где можно воспользоваться преимуществами простоты настройки и возможностью быстро развернуть ферму, чтобы использовать новую емкость или оптимизировать основные функции.

## Простая ферма SharePoint для разработки и тестирования

Для виртуальных машин, созданных в службе управления, используйте функцию [фермы серверов SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) портала предварительной версии Azure, чтобы создать базовую ферму для разработки и тестирования для веб-сайта SharePoint с доступом из Интернета.

Автоматически созданная среда состоит из трех серверов для контроллера домена, сервера SQL Server и сервера SharePoint в облачной виртуальной сети Azure.

Чтобы создать подобную конфигурацию с виртуальными машинами, созданными в диспетчере ресурсов, используйте шаблон. См. раздел [Развертывание трехсерверной фермы SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

## Высокодоступная ферма SharePoint для разработки и тестирования

Для виртуальных машин, созданных в службе управления, используйте функцию [фермы серверов SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) портала предварительной версии Azure, чтобы создать высокодоступную ферму SharePoint для разработки и тестирования для веб-сайта SharePoint с доступом из Интернета.

Автоматически созданная среда состоит из девяти серверов в облачной виртуальной сети Azure: два для контроллеров домена, три для кластера SQL Server, два сервера SharePoint уровня приложений и два сервера SharePoint уровня Интернета.

Чтобы создать подобную конфигурацию с виртуальными машинами, созданными в диспетчере ресурсов, используйте шаблон. См. раздел [Развертывание девятисерверной фермы SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

## Гибридная облачная ферма для разработки и тестирования

С помощью [фермы интрасети SharePoint в гибридной облачной среде разработки и тестирования](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) можно создать имитацию гибридной облачной конфигурации, в которой размещается простая двухуровневая ферма SharePoint, которую можно использовать для тестирования фермы интрасети SharePoint, размещенной в Azure из расположения в Интернете.

В этой конфигурации используются виртуальные машины, созданные в управлении службами.

## Высокодоступная рабочая ферма SharePoint в интрасети

Развертывая [SharePoint 2013 с группами доступности AlwaysOn SQL Server в Azure](virtual-machines-workload-intranet-sharepoint-overview.md), можно создать готовую высокодоступную ферму интрасети SharePoint Server 2013 в Azure.

В этой конфигурации используются виртуальные машины, созданные в управлении службами.

## Дополнительные ресурсы

Дополнительную информацию о SharePoint в Azure см. в следующих ресурсах:

- [Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

- [Веб-сайты в Microsoft Azure с использованием SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

- [Аварийное восстановление SharePoint Server 2013 в Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

- [Использование службы Microsoft Azure Active Directory для проверки подлинности в SharePoint 2013](https://technet.microsoft.com/library/dn635311.aspx)

- [Развертывание службы синхронизации каталогов Office 365 (DirSync) в Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=August15_HO6-->