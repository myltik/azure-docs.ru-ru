<properties 
	pageTitle="Развертывание бизнес-приложения, размещенного в Azure | Microsoft Azure" 
	description="Высокодоступное специализированное веб-приложение с группами доступности AlwaysOn для SQL Server разворачивается в Azure в пять этапов." 
	documentationCenter=""
	services="virtual-machines" 
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
	ms.date="11/09/2015" 
	ms.author="josephd"/>

# Развертывание высокодоступных бизнес-приложений в Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

В этой статье приведены пошаговые инструкции по развертыванию в службах инфраструктуры Azure высокодоступных специализированных веб-приложений для интрасети с группами доступности AlwaysOn для SQL Server. Приложение размещается на следующих компьютерах:

- два веб-сервера;
- два сервера баз данных;
- Один сервер узла большинства кластера
- два контроллера домена.

Вот схема конфигурации (для каждого сервера указаны шаблонные имена):

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
Для каждой роли высокий уровень доступности обеспечивают как минимум два компьютера. Все виртуальные машины находятся в одном расположении Azure (также называется регионом). Каждая группа виртуальных машин, выполняющих определенную роль, находится в собственной группе доступности.

Ниже перечислены этапы развертывания этой конфигурации.

- [Этап 1. Настройка Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md). Создание учетных записей хранения, групп доступности и распределенной виртуальной сети.
- [Второй этап: настройка контроллеров домена](virtual-machines-workload-high-availability-LOB-application-phase2.md). Создание и настройка реплик контроллеров домена доменных служб Active (AD DS).
- [Третий этап: настройка инфраструктуры SQL Server](virtual-machines-workload-high-availability-LOB-application-phase3.md). Создание и настройка виртуальных машин с SQL Server, создание кластера и включение групп доступности AlwaysOn для SQL Server.
- [Четвертый этап: настройка веб-серверов](virtual-machines-workload-high-availability-LOB-application-phase4.md). Создание и настройка двух виртуальных машин для веб-серверов.
- [Пятый этап: добавление баз данных приложения в группу доступности AlwaysOn для SQL Server](virtual-machines-workload-high-availability-LOB-application-phase5.md). Подготовка баз данных бизнес-приложения и их добавление в группу доступности AlwaysOn для SQL Server.

Схема этого развертывания дополняет [схемы проектов архитектуры для бизнес-приложений](http://msdn.microsoft.com/dn630664) и содержит наиболее актуальные рекомендации.

Это стандартная заранее определенная архитектура. Помните о перечисленных ниже моментах.

- Чтобы создать инфраструктуру приложения под свои нужды, необходимо внести коррективы в этапы 3–5. Для этого требуется достаточный опыт внедрения специализированных веб-приложений. 
- Если у вас уже есть гибридное облачное развертывание Azure, внесите необходимые изменения в этапы 1–2 (или пропустите их). Таким образом вы сможете разместить виртуальные машины для нового приложения в соответствующей подсети.
- Все серверы расположены в одной подсети виртуальной сети Azure. Для реализации дополнительных мер безопасности, эквивалентных изоляции подсетей, можно воспользоваться [группами безопасности сети](../virtual-networks/virtual-networks-nsg.md).

Инструкции по созданию среды для разработки или тестирования либо экспериментальной проверки этой конфигурации см. в статье [Настройка веб бизнес-приложения в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md).

Дополнительные сведения о проектировании рабочих нагрузок в ИТ для Azure см. в [руководстве по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md).

## Дальнейшие действия

Начальные действия по настройке этой рабочей нагрузки описаны в разделе [Этап 1. Настройка Azure](virtual-machines-workload-high-availability-LOB-application-phase1.md).

## Дополнительные ресурсы

[Архитектурный проект бизнес-приложений](http://msdn.microsoft.com/dn630664)

[Настройка веб-бизнес-приложения в гибридном облаке для тестирования](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Руководство по реализации служб инфраструктуры Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Рабочая нагрузка служб инфраструктуры Azure: ферма SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Nov15_HO3-->