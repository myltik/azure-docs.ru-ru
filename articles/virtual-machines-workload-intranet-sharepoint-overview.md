<properties 
	pageTitle="Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure" 
	description="Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure выполняется в пять этапов." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Развертывание среды SharePoint с группами доступности AlwaysOn для SQL Server на платформе Azure

В этой статье приведены пошаговые инструкции для развертывания фермы SharePoint 2013 для интрасети с группами доступности AlwaysOn для SQL Server на базе служб инфраструктуры Azure. В состав фермы входят перечисленные ниже компьютеры.

- Два веб-сервера SharePoint
- Два сервера приложений SharePoint
- Два сервера баз данных
- Один сервер узла большинства кластера
- Два контроллера домена

Вот схема конфигурации (для каждого сервера указаны шаблонные имена):

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)
 
Два компьютера для каждой роли обеспечивают высокий уровень доступности. Все виртуальные машины расположены в одном регионе. Каждая группа виртуальных машин, выполняющих определенную роль, находится в собственной группе доступности.

Ниже перечислены этапы развертывания этой конфигурации.

- [Этап 1. Настройка Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Создание учетной записи хранения, облачных служб и распределенной виртуальной сети.
- [Этап 2. Настройка контроллеров домена](virtual-machines-workload-intranet-sharepoint-phase2.md). Создание и настройка реплик контроллеров домена доменных служб Active (AD DS).
- [Этап 3. Настройка инфраструктуры сервера SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Создание и настройка виртуальных машин SQL Server, их подготовка для работы со средой SharePoint и создание кластера.
- [Этап 4. Настройка серверов SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Создание и настройка четырех виртуальных машин SharePoint.
- [Этап 5. Создание группы доступности и добавление баз данных SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Подготовка баз данных и создание группы доступности AlwaysOn для SQL Server.

Это развертывание фермы SharePoint с группами доступности AlwaysOn для SQL Server соответствует [этой схеме](http://go.microsoft.com/fwlink/?LinkId=394788) и выполняется в соответствии с актуальными рекомендациями.

Эта схема представляет собой пошаговое руководство по созданию функциональной высокодоступной фермы SharePoint в интрасети на базе служб инфраструктуры Azure в соответствии с определенной архитектурой. Дополнительные советы и рекомендации по развертыванию SharePoint 2013 в среде Azure на базе различных архитектур см. в статье [Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Помните о перечисленных ниже моментах.

- Если вы являетесь опытным внедренцем SharePoint, внесите необходимые изменения в этапы с 3-го по 5-й в соответствии с собственными требованиями к ферме. 
- Если у вас уже есть гибридное облачное развертывание Azure, внесите необходимые изменения в этапы с 1-го по 2-й (или пропустите их) для создания новой фермы SharePoint в соответствующей подсети.
- Все серверы расположены в одной подсети виртуальной сети Azure. Для реализации дополнительных мер безопасности, эквивалентных изоляции подсетей, можно воспользоваться [группами безопасности сети](https://msdn.microsoft.com/library/azure/dn848316.aspx).

Инструкции по созданию среды для разработки или тестирования либо экспериментальной проверке этой конфигурации см. в статье [Настройка фермы SharePoint интрасети в гибридном облаке для тестирования](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Дополнительные сведения о среде SharePoint с группами доступности AlwaysOn для SQL Server см. в статье [Настройка группы доступности AlwaysOn SQL Server 2012 для SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Дальнейшее действие

Начальные действия по настройке этой рабочей нагрузки описаны в разделе [Этап 1. Настройка Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).


## Дополнительные ресурсы

[Среда SharePoint с группами доступности AlwaysOn для SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788)

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Фермы SharePoint, размещенные в службах инфраструктуры Azure](virtual-machines-sharepoint-infrastructure-services.md)

<!--HONumber=54-->