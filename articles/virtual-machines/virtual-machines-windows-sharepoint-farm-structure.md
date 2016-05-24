<properties
	pageTitle="Фермы SharePoint Server 2013 в Azure | Microsoft Azure"
	description="Поиск статей, в которых описывается настройка среды разработки и тестирования или рабочая ферма SharePoint Server 2013 в Microsoft Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# Фермы SharePoint, размещенные в службах инфраструктуры Azure

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Настройте свою первую или очередную ферму SharePoint Server 2013 для разработки и тестирования или производства в службах инфраструктуры Microsoft Azure, где можно воспользоваться преимуществами простоты настройки и возможностью быстро развернуть ферму, чтобы использовать новую емкость или оптимизировать основные функции.

## Простая ферма SharePoint для разработки и тестирования

Автоматически созданная среда состоит из трех серверов в облачной виртуальной сети Azure: контроллера домена, сервера SQL Server и сервера SharePoint.

См. пункт [Ферма без высокой доступности SharePoint 2013](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) в Azure Marketplace на портале Azure. Он создает простую ферму для разработки и тестирования для веб-сайта SharePoint с выходом в Интернет. Дополнительные сведения см. в статье [Создание ферм серверов SharePoint](virtual-machines-windows-sharepoint-farm.md).

Также можно использовать шаблон диспетчера ресурсов Azure. См. статью [Развертывание популярных платформ приложений с помощью шаблонов Azure Resource Manager](virtual-machines-linux-app-frameworks.md).

> [AZURE.NOTE] Пункт **Ферма серверов SharePoint** удален из Azure Marketplace на портале Azure.

## Высокодоступная ферма SharePoint для разработки и тестирования

Эта автоматически созданная среда состоит из девяти серверов в облачной виртуальной сети Azure: два для контроллеров домена, три для кластера SQL Server, два сервера SharePoint уровня приложений и два сервера SharePoint уровня Интернета.

См. пункт [Ферма SharePoint 2013 с высоким уровнем доступности](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) в Azure Marketplace на портале Azure. Он создает ферму высокой доступности для разработки и тестирования для веб-сайта SharePoint с выходом в Интернет. Дополнительные сведения см. в статье [Создание ферм серверов SharePoint](virtual-machines-windows-sharepoint-farm.md).

Также можно использовать шаблон диспетчера ресурсов Azure. См. статью [Развертывание девятисерверной фермы SharePoint](virtual-machines-windows-app-frameworks.md#deploy-a-nine-server-sharepoint-farm).

> [AZURE.NOTE] Пункт **Ферма серверов SharePoint** удален из Azure Marketplace на портале Azure.

## Гибридная облачная ферма для разработки и тестирования

С помощью [фермы интрасети SharePoint в гибридной облачной среде разработки и тестирования](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md) можно создать имитацию гибридной облачной конфигурации, в которой размещается простая двухуровневая ферма SharePoint, которую можно использовать для тестирования фермы интрасети SharePoint, размещенной в Azure из расположения в Интернете.

## Высокодоступная рабочая ферма SharePoint в интрасети

Развертывая [SharePoint 2013 с группами доступности AlwaysOn сервера SQL Server в Azure](virtual-machines-windows-sp-intranet-overview.md), можно создать готовую высокодоступную ферму интрасети SharePoint Server 2013 в Azure.

## Дальнейшее действие

- Ознакомьтесь с дополнительными конфигурациями [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) в службах инфраструктуры Azure.

<!---HONumber=AcomDC_0511_2016-->