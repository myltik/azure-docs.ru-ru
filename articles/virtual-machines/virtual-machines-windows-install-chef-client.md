<properties 
	pageTitle="Сведения о Chef и виртуальных машинах Azure" 
	description="Описывается установка и настройка Chef на виртуальной машине в Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="kathydav"/>

#Сведения о Chef и виртуальных машинах Azure

Chef предоставляет систему автоматизации для создания, развертывания инфраструктуры и управления ею. Ресурсы управляются с помощью рецептов, которые являются многократно используемыми определениями, предоставляющими инструкции для таких задач, как настройка веб-сервера.   

Chef является системой "клиент-сервер". Варианты использования сервера Chef см. в статье [Выбор варианта установки](http://www.getchef.com/chef/choose-your-version/). Для установки клиента потребуются сведения о сервере Chef. 

Существуют следующие варианты установки клиента Chef на виртуальной машине Azure:

- Использование портала управления Azure для установки клиента Chef при создании виртуальной машины под управлением Windows Server 2012 или Windows Server 2012 R2. Инструкции см. на [портале управления Microsoft Azure](https://docs.chef.io/azure_portal.html).
- Использование Windows PowerShell для установки клиента Chef на существующей виртуальной машине. Пример [скрипт](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) доступен на сайте Github.
- Использование подключаемого модуля Chef, [knife-azure](http://docs.getchef.com/plugin_knife_azure.html), для создания экземпляра виртуальной машины и установки клиента Chef.   


##Дополнительные ресурсы
[Chef и Microsoft Azure]

[Как войти в виртуальную машину под управлением Windows Server]

[Как войти в виртуальную машину под управлением Linux]

[Управление расширениями]

<!--Link references-->
[Chef и Microsoft Azure]: http://www.getchef.com/solutions/azure/
[Как войти в виртуальную машину под управлением Windows Server]: ../virtual-machines-log-on-windows-server/
[Как войти в виртуальную машину под управлением Linux]: ../virtual-machines-linux-how-to-log-on
[Управление расширениями]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=47-->
 