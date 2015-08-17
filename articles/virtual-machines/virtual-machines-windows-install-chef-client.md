<properties
	pageTitle="О Chef и виртуальных машинах Azure"
	description="Описывает, как установить и настроить Chef в виртуальной машине в Azure"
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
	ms.date="05/20/2015"
	ms.author="kathydav"/>

#О Chef и виртуальных машинах Azure

Chef предоставляет систему автоматизации для построения, развертывания и управления инфраструктурой. Ресурсы управляются с помощью рецептов, которые являются многократно используемыми определениями, предоставляющими инструкции для таких задач, как настройка веб-сервера.

Chef является системой "клиент-сервер". Варианты использования сервера Chef см. в разделе [Выбор варианта установки](http://www.getchef.com/chef/choose-your-version/). Для установки клиента потребуются сведения о сервере Chef.

Существуют следующие варианты установки клиента Chef на виртуальной машине Azure:

- Использование портала Azure для установки клиента Chef при создании виртуальной машины под управлением Windows Server 2012 или Windows Server 2012 R2. Указания см. на [портале Azure](https://docs.chef.io/azure_portal.html).
- Использование Windows PowerShell для установки клиента Chef на существующей виртуальной машине. Пример [скрипт](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) доступен на сайте Github.
- Использование подключаемого модуля Chef [knife-azure](http://docs.getchef.com/plugin_knife_azure.html) для создания экземпляра виртуальной машины и установки клиента Chef.


##Дополнительные ресурсы
[Chef и Microsoft Azure]

[Вход в виртуальную машину под управлением Windows Server]

[Вход в виртуальную машину под управлением ОС Linux]

[Управление расширениями]

<!--Link references-->
[Chef и Microsoft Azure]: http://www.getchef.com/solutions/azure/
[Вход в виртуальную машину под управлением Windows Server]: virtual-machines-log-on-windows-server.md
[Вход в виртуальную машину под управлением ОС Linux]: virtual-machines-linux-how-to-log-on.md
[Управление расширениями]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=August15_HO6-->