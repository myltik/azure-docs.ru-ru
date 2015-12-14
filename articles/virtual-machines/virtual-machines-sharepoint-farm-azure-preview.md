<properties
	pageTitle="Создание ферм серверов SharePoint | Microsoft Azure"
	description="Быстро создавайте фермы SharePoint Server 2013, как базовые, так и с высоким уровнем доступности, используя Marketplace портала Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="josephd"/>

# Создание ферм серверов SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]классической модели.

С помощью Marketplace на портале Microsoft Azure можно быстро создавать предварительно настроенные фермы SharePoint Server 2013. Это позволяет сэкономить много времени при необходимости использовать базовую или высокодоступную ферму SharePoint в среде разработки и тестирования или в ходе оценки SharePoint Server 2013 в качестве решения для совместной работы в рамках организации.

> [AZURE.NOTE]Пункт **Ферма серверов SharePoint** удален из Azure Marketplace на портале Azure.

Базовая ферма SharePoint в этой конфигурации состоит из трех виртуальных машин.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

Ферму такой конфигурации можно использовать в качестве упрощенной установки для разработки приложений SharePoint или первоначальной оценки SharePoint 2013.

Создание базовой фермы SharePoint (из трех серверов):

1. Щелкните [здесь](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Щелкните **Развернуть**.
3. На панели **Ферма без высокой доступности SharePoint 2013** нажмите кнопку **Создать**.
4. Укажите параметры в 7 шагах на панели **Создание фермы без высокой доступности SharePoint 2013** и нажмите кнопку **Создать**.

Ферма SharePoint с высоким уровнем доступности в этой конфигурации состоит из девяти виртуальных машин.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

Ферму такой конфигурации можно использовать для тестирования более высоких нагрузок клиента, высокой доступности внешнего сайта SharePoint и SQL Server AlwaysOn для фермы SharePoint. Кроме того, такая конфигурация подходит для разработки приложений SharePoint в высокодоступной среде.

Создание фермы высокой доступности SharePoint (9 серверов):

1. Щелкните [здесь](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Щелкните **Развернуть**.
3. На панели **Ферма высокой доступности SharePoint 2013** нажмите кнопку **Создать**.
4. Укажите параметры в 7 шагах на панели **Создание фермы высокой доступности SharePoint 2013** и нажмите кнопку **Создать**.

> [AZURE.NOTE]Корпорация Майкрософт выпустила SharePoint Server 2016 IT (предварительная версия). Для облегчения установки и тестирования предварительной версии вы можете воспользоваться образом с SharePoint Server 2016 IT (предварительная версия) и предустановленными обязательными компонентами из коллекции виртуальных машин Azure. Дополнительные сведения можно найти в статье [Тестирование SharePoint Server 2016 IT (предварительная версия) в Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Управление фермами SharePoint

Для администрирования серверов этих ферм можно использовать подключения к удаленному рабочему столу. Дополнительные сведения см. в статье [Вход на виртуальную машину](virtual-machines-windows-tutorial.md#log-on-to-the-virtual-machine).

На сайте Центра администрирования SharePoint можно настраивать "Мои сайты", приложения SharePoint и другие функции. Более подробную информацию см. в разделе [Настройка SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

> [AZURE.NOTE]Портал Azure создает обе эти фермы только в облачной виртуальной сети с веб-присутствием. Подключение VPN типа "сеть-сеть" или ExpressRoute обратно к сети организации не создается.

## Дополнительные ресурсы

[Фермы SharePoint, размещенные в службах инфраструктуры Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!---HONumber=AcomDC_1203_2015-->