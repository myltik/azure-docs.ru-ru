<properties
	pageTitle="Создание ферм серверов SharePoint | Microsoft Azure"
	description="Быстрое создание новой фермы SharePoint 2013 или SharePoint 2016 в Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

.<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="josephd"/>

# Создание ферм серверов SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] классической модели.

## Фермы SharePoint 2013

С помощью Marketplace на портале Microsoft Azure можно быстро создавать предварительно настроенные фермы SharePoint Server 2013. Такой подход может сэкономить много времени, когда в среде разработки и тестирования нужна базовая или высокодоступная ферма SharePoint или когда SharePoint Server 2013 рассматривается в качестве решения для совместной работы в рамках организации.

> [AZURE.NOTE] Пункт **Ферма серверов SharePoint** удален из Azure Marketplace на портале Azure. Он был заменен на **SharePoint 2013 non-HA Farm** (Ферма SharePoint 2013 без высокой доступности) и **SharePoint 2013 HA Farm** (Высокодоступная ферма SharePoint 2013).

Базовая ферма SharePoint в этой конфигурации состоит из трех виртуальных машин.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Ферму такой конфигурации можно использовать в качестве упрощенной установки для разработки приложений SharePoint или первоначальной оценки SharePoint 2013.

Создание базовой фермы SharePoint (из трех серверов):

1. Щелкните [здесь](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Щелкните **Развернуть**.
3. На панели **Ферма без высокой доступности SharePoint 2013** нажмите кнопку **Создать**.
4. На панели **Create SharePoint 2013 non-HA Farm** (Создание фермы SharePoint 2013 без высокой доступности) укажите параметры и нажмите кнопку **Создать**.

Ферма SharePoint с высоким уровнем доступности в этой конфигурации состоит из девяти виртуальных машин.

.![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Ферму такой конфигурации можно использовать для тестирования более высоких клиентских нагрузок, высокой доступности внешнего сайта SharePoint и групп доступности SQL Server AlwaysOn для фермы SharePoint. Кроме того, такая конфигурация подходит для разработки приложений SharePoint в высокодоступной среде.

Создание фермы высокой доступности SharePoint (9 серверов):

1. Щелкните [здесь](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Щелкните **Развернуть**.
3. На панели **Ферма высокой доступности SharePoint 2013** нажмите кнопку **Создать**.
4. Укажите параметры в семи разделах в области **Create SharePoint 2013 HA Farm** (Создание высокодоступной фермы SharePoint 2013) и нажмите кнопку **Создать**.

> [AZURE.NOTE] Нельзя создать **ферму SharePoint 2013 без высокой доступности** или **высокодоступную ферму SharePoint 2013** с помощью бесплатной пробной версии Azure.

Портал Azure создает обе эти фермы только в облачной виртуальной сети с веб-присутствием. Подключение VPN типа "сеть-сеть" или ExpressRoute обратно к сети организации не создается.

## Фермы SharePoint 2016

Инструкции по созданию описанной ниже фермы SharePoint 2016 с одним сервером см. в [этой статье](https://technet.microsoft.com/library/mt723354.aspx).

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## Управление фермами SharePoint

Для администрирования серверов этих ферм можно использовать подключения к удаленному рабочему столу. Дополнительные сведения см. в статье [Вход на виртуальную машину](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

На сайте Центра администрирования SharePoint можно настраивать "Мои сайты", приложения SharePoint и другие функции. Дополнительные сведения см. в статье [Настройка SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

## Дальнейшие действия

- Ознакомьтесь с дополнительными [конфигурациями SharePoint](https://technet.microsoft.com/library/dn635309.aspx) в службах инфраструктуры Azure.

<!---HONumber=AcomDC_0810_2016-->