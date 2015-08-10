<properties
	pageTitle="Сведения о Puppet и виртуальных машинах Azure"
	description="В статье описывается установка и настройка Puppet на виртуальной машине в Azure"
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

#Сведения о Puppet и виртуальных машинах Azure


<p>Puppet Enterprise — это программное обеспечение автоматизации для создания, развертывания инфраструктуры и управления ею. С его помощью можно управлять жизненным циклом ИТ-инфраструктуры, в том числе выполнять обнаружение, подготовку к работе, управление конфигурацией ОС и приложений, оркестрацию и создание отчетов.

Puppet является системой "клиент-сервер". С помощью Azure можно установить и Puppet Master, и Puppet Enterprise Agent.

- Puppet Master доступен в виде предварительно настроенного образа, установленного на сервере Ubuntu. Можно также установить Puppet Enterprise на существующий сервер, но для начала работы проще всего воспользоваться образом. Для установки агента потребуются сведения о сервере.

- Puppet Enterprise Agent доступен в виде расширения виртуальной машины, которое можно устанавливать при создании виртуальной машины или на существующую виртуальную машину.

Для получения инструкций загрузите руководство по началу работы со страницы [Microsoft Windows и Azure](http://puppetlabs.com/solutions/microsoft).


##Дополнительные ресурсы
[Новые способы интеграции с Microsoft Azure и Visual Studio]

[Вход в виртуальную машину под управлением Windows Server]

[Вход в виртуальную машину под управлением ОС Linux]

[Управление расширениями]

<!--Link references-->
[Новые способы интеграции с Microsoft Azure и Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Вход в виртуальную машину под управлением Windows Server]: virtual-machines-log-on-windows-server.md
[Вход в виртуальную машину под управлением ОС Linux]: virtual-machines-linux-how-to-log-on.md
[Azure VM extensions and features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=July15_HO5-->