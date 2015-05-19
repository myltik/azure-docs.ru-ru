<properties 
	pageTitle="Фермы SharePoint, размещенные в службах инфраструктуры Azure" 
	description="Ознакомьтесь с основными разделами, в которых описывается настройка фермы SharePoint 2013 для разработки и тестирования или производства в службах инфраструктуры Azure." 
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
	ms.date="04/06/2015" 
	ms.author="josephd"/>

# Фермы SharePoint, размещенные в службах инфраструктуры Azure

Настройте свою первую или очередную ферму SharePoint для разработки и тестирования или производства в службах инфраструктуры Microsoft Azure, где можно воспользоваться преимуществами простоты настройки и возможностью быстро развернуть ферму, чтобы использовать новую емкость или оптимизировать основные функции.

## Простая ферма SharePoint для разработки и тестирования 

Вы можете использовать шаблон [фермы серверов SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) на портале предварительной версии Azure, чтобы создать простую ферму для разработки и тестирования для веб-сайта SharePoint с выходом в Интернет.

Автоматически созданная среда состоит из трех серверов для контроллера домена, сервера SQL Server и сервера SharePoint в облачной виртуальной сети Azure.

## Высокодоступная ферма SharePoint для разработки и тестирования

Вы можете также использовать шаблон [фермы серверов SharePoint](virtual-machines-sharepoint-farm-azure-preview.md) на портале предварительной версии Azure, чтобы создать ферму с высоким уровнем доступности для разработки и тестирования для веб-сайта SharePoint с выходом в Интернет.

Автоматически созданная среда состоит из девяти серверов в облачной виртуальной сети Azure: два для контроллеров домена, три для кластера SQL Server, два сервера SharePoint уровня приложений и два сервера SharePoint уровня Интернета.

## Гибридная облачная ферма для разработки и тестирования

С помощью [фермы интрасети SharePoint в гибридной облачной среде разработки и тестирования](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md) можно создать имитацию гибридной облачной конфигурации, в которой размещается простая двухуровневая ферма SharePoint, которую можно использовать для тестирования фермы интрасети SharePoint, размещенной в Azure из расположения в Интернете.

## Высокодоступная рабочая ферма интрасети SharePoint

[Развертывая SharePoint 2013 с группами доступности AlwaysOn SQL Server в Azure](https://msdn.microsoft.com/library/dn275959.aspx), можно построить готовую высокодоступную ферму интрасети SharePoint Server 2013 в Azure.

## Дополнительные ресурсы

[SharePoint Server в службах инфраструктуры Azure](https://msdn.microsoft.com/library/dn275955.aspx)

[Планирование SharePoint 2013 в службах инфраструктуры Azure](https://msdn.microsoft.com/library/dn275958.aspx)

[Архитектуры Microsoft Azure для SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=52-->
