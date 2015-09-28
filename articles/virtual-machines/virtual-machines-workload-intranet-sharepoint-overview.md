<properties
	pageTitle="Развертывание фермы SharePoint Server 2013 | Microsoft Azure"
	description="Высокодоступные фермы SharePoint Server 2013 с использованием групп доступности AlwaysOn для SQL Server развертываются в Azure в пять этапов."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="josephd"/>

# Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс создания ресурсов с помощью классической модели развертывания.

В этом разделе приведены пошаговые инструкции по развертыванию фермы SharePoint 2013 для интрасети с группами доступности AlwaysOn для SQL Server с помощью классической модели развертывания. В состав фермы входят перечисленные ниже компьютеры.

- Два веб-сервера SharePoint
- Два сервера приложений SharePoint
- Два сервера баз данных
- Один сервер узла большинства кластера
- Два контроллера домена

Вот схема конфигурации (для каждого сервера указаны имена-заполнители):

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

Два компьютера для каждой роли обеспечивают высокий уровень доступности. Все виртуальные машины расположены в одном регионе. Каждая группа виртуальных машин, выполняющих определенную роль, находится в собственной группе доступности.

Ниже перечислены этапы развертывания этой конфигурации.

- [Этап 1. Настройка Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Создание учетной записи хранения, облачных служб и распределенной виртуальной сети.
- [Второй этап: настройка контроллеров домена](virtual-machines-workload-intranet-sharepoint-phase2.md). Создание и настройка реплик контроллеров домена доменных служб Active (AD DS).
- [Третий этап: настройка инфраструктуры SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Создание и настройка виртуальных машин SQL Server, их подготовка для работы со средой SharePoint и создание кластера.
- [Четвертый этап: настройка серверов SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Создание и настройка четырех виртуальных машин SharePoint.
- [ Пятый этап: создание группы доступности и добавление баз данных SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Подготовка баз данных и создание группы доступности AlwaysOn для SQL Server.

Данное развертывание фермы SharePoint с группами доступности AlwaysOn для SQL Server соответствует [этой схеме](http://go.microsoft.com/fwlink/?LinkId=394788) и выполняется в соответствии с актуальными рекомендациями.

Эта схема представляет собой пошаговое руководство по созданию функциональной высокодоступной фермы SharePoint в интрасети на базе служб инфраструктуры Azure в соответствии с определенной архитектурой. Дополнительные советы и рекомендации по развертыванию SharePoint 2013 в среде Azure на базе различных архитектур см. в статье [Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Помните о перечисленных ниже моментах.

- Если вы являетесь опытным специалистом по внедрению SharePoint, внесите необходимые изменения в этапы 3–5 в соответствии с собственными требованиями к ферме.
- Если у вас уже есть гибридное облачное развертывание Azure, внесите необходимые изменения в этапы 1–2 (или пропустите их) для создания новой фермы SharePoint в соответствующей подсети.
- Все серверы расположены в одной подсети виртуальной сети Azure. Для реализации дополнительных мер безопасности, эквивалентных изоляции подсетей, можно воспользоваться [группами безопасности сети](virtual-networks-nsg.md).

Инструкции по созданию среды для разработки или тестирования либо экспериментальной проверке этой конфигурации см. в статье [Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Дополнительные сведения о среде SharePoint с группами доступности AlwaysOn для SQL Server см. в статье [Настройка групп обеспечения доступности SQL Server 2012 AlwaysOn для SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

> [AZURE.NOTE]Корпорация Майкрософт выпустила SharePoint Server 2016 IT (предварительная версия). Для облегчения установки и тестирования предварительной версии вы можете воспользоваться образом с SharePoint Server 2016 IT (предварительная версия) и предустановленными обязательными компонентами из коллекции виртуальных машин Azure. Дополнительные сведения можно найти в статье [Тестирование SharePoint Server 2016 IT (предварительная версия) в Azure](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Дальнейшие действия

Начальные действия по настройке этой рабочей нагрузки описаны в разделе [Этап 1. Настройка Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).


## Дополнительные ресурсы

[Инфографика SharePoint с SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Фермы SharePoint, размещенные в службах инфраструктуры Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Службы инфраструктуры Azure: высокодоступное бизнес-приложение](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Sept15_HO3-->