<properties
	pageTitle="Фермы SharePoint Server 2013 в Azure | Microsoft Azure"
	description="Поиск статей, в которых описывается настройка среды разработки и тестирования или рабочая ферма SharePoint Server 2013 в Microsoft Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Фермы SharePoint, размещенные в службах инфраструктуры Azure

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Настройте свою первую или очередную ферму SharePoint для разработки и тестирования или производства в службах инфраструктуры Microsoft Azure, где можно воспользоваться преимуществами простоты настройки и возможностью быстро развернуть ферму, чтобы использовать новую емкость или оптимизировать основные функции.

> [AZURE.NOTE]Корпорация Майкрософт выпустила SharePoint Server 2016 IT (предварительная версия). Для облегчения установки и тестирования предварительной версии вы можете воспользоваться образом с SharePoint Server 2016 IT (предварительная версия) и предустановленными обязательными компонентами из коллекции виртуальных машин Azure. Дополнительные сведения можно найти в статье [Тестирование SharePoint Server 2016 IT (предварительная версия) в Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Простая ферма SharePoint для разработки и тестирования

Для работы с виртуальными машинами, созданными с помощью модели развертывания диспетчера ресурсов, можно использовать элемент [Ферма SharePoint 2013 non-HA](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) в Azure Marketplace на портале предварительной версии Azure. Он создает простую ферму для разработки и тестирования для веб-сайта SharePoint с выходом в Интернет.

Также можно использовать шаблон диспетчера ресурсов Azure. См. статью [Развертывание трехсерверной фермы SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm).

Автоматически созданная среда состоит из трех серверов для контроллера домена, сервера SQL Server и сервера SharePoint в облачной виртуальной сети Azure.

Чтобы создать подобную конфигурацию на основе классической модели развертывания, используйте элемент [Ферма серверов SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) в Azure Marketplace на портале предварительной версии Azure.


## Высокодоступная ферма SharePoint для разработки и тестирования

Для работы с виртуальными машинами, созданными с помощью модели развертывания диспетчера ресурсов, можно использовать элемент [Ферма SharePoint 2013 HA](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) в Azure Marketplace на портале предварительной версии Azure. Он создает высокодоступную ферму для веб-сайта SharePoint с выходом в Интернет.

Также можно использовать шаблон диспетчера ресурсов Azure. См. статью [Развертывание девятисерверной фермы SharePoint](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm).

Автоматически созданная среда состоит из девяти серверов в облачной виртуальной сети Azure: два для контроллеров домена, три для кластера SQL Server, два сервера SharePoint уровня приложений и два сервера SharePoint уровня Интернета.

Чтобы создать подобную конфигурацию на основе классической модели развертывания, используйте элемент [Ферма серверов SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) в Azure Marketplace на портале предварительной версии Azure.


## Гибридная облачная ферма для разработки и тестирования

С помощью [фермы интрасети SharePoint в гибридной облачной среде разработки и тестирования](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) можно создать имитацию гибридной облачной конфигурации, в которой размещается простая двухуровневая ферма SharePoint, которую можно использовать для тестирования фермы интрасети SharePoint, размещенной в Azure из расположения в Интернете.

В этой конфигурации используются классические виртуальные машины.

## Высокодоступная рабочая ферма SharePoint в интрасети

Развертывая [SharePoint 2013 с группами доступности AlwaysOn сервера SQL Server в Azure](virtual-machines-workload-intranet-sharepoint-overview.md), можно создать готовую высокодоступную ферму интрасети SharePoint Server 2013 в Azure.

В этой конфигурации используются классические виртуальные машины.

## Дополнительные ресурсы

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Веб-сайты в Microsoft Azure с использованием SharePoint Server 2013](https://technet.microsoft.com/library/dn635307.aspx)

[Аварийное восстановление SharePoint Server 2013 в Microsoft Azure](https://technet.microsoft.com/library/dn635313.aspx)

[Использование службы Microsoft Azure Active Directory для проверки подлинности в SharePoint 2013](https://technet.microsoft.com/library/dn635311.aspx)

[Развертывание службы синхронизации каталогов Office 365 (DirSync) в Microsoft Azure](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=Oct15_HO4-->