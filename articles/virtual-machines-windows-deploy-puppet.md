<properties title="About Puppet and Azure Virtual Machines" pageTitle="About Puppet and Azure Virtual Machines" description="Describes installing and configuring Puppet on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# О Puppet и виртуальных машинах Azure

Puppet Enterprise — это программное обеспечение автоматизации для построения, развертывания и управления инфраструктурой. Его можно использовать для управления жизненным циклом ИТ-инфраструктуры, включая обнаружение, подготовку, управление конфигурацией ОС и приложений, оркестрацию и создание отчетов.

Puppet является системой "клиент-сервер". Для установки с помощью Azure доступны Puppet Master и Puppet Enterprise Agent.

-   Puppet Master доступен в виде предварительно настроенного образа, установленного на сервере Ubuntu. Можно также установить Puppet Enterprise на существующий сервер, но для начала работы проще всего воспользоваться образом. Для установки агента потребуются сведения о сервере.
-   Puppet Enterprise Agent доступен в виде расширения виртуальной машины, которое можно устанавливать при создании виртуальной машины или на существующую виртуальную машину.

Для получения инструкций загрузите руководство по началу работы со страницы [Microsoft Windows и Azure][Microsoft Windows и Azure].

## Дополнительные ресурсы

[Новые интеграции с Microsoft Azure и Visual Studio][Новые интеграции с Microsoft Azure и Visual Studio]

[Как войти в виртуальную машину под управлением Windows Server][Как войти в виртуальную машину под управлением Windows Server]

[Как войти в виртуальную машину под управлением Linux][Как войти в виртуальную машину под управлением Linux]

[Управление расширениями][Управление расширениями]

<!--Link references-->

  [Microsoft Windows и Azure]: http://puppetlabs.com/solutions/microsoft
  [Новые интеграции с Microsoft Azure и Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
  [Как войти в виртуальную машину под управлением Windows Server]: ../virtual-machines-log-on-windows-server/
  [Как войти в виртуальную машину под управлением Linux]: ../virtual-machines-linux-how-to-log-on
  [Управление расширениями]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
