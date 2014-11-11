<properties title="About Chef and Azure Virtual Machines" pageTitle="About Chef and Azure Virtual Machines" description="Describes installing and configuring Chef on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# О Chef и виртуальных машинах Azure

Chef предоставляет систему автоматизации для построения, развертывания и управления инфраструктурой. Ресурсы управляются с помощью рецептов, которые являются многократно используемыми определениями, предоставляющими инструкции для таких задач, как настройка веб-сервера.

Chef является системой "клиент-сервер". Варианты использования сервера Chef см. в статье [Choose your installation][Choose your installation] (Выбор варианта установки). Для установки клиента потребуются сведения о сервере Chef. Существуют следующие варианты установки клиента Chef на виртуальной машине Azure.

-   Использование портала управления для установки клиента Chef при создании виртуальной машины под управлением Windows Server 2012 или Windows Server 2012 R2. Инструкции см. на [портале Microsoft Azure][портале Microsoft Azure].
-   Использование Windows PowerShell для установки клиента Chef на существующей виртуальной машине. На сайте Github имеется образец [скрипта][скрипта].
-   Использование подключаемого модуля Chef [knife-azure][knife-azure] для создания экземпляра виртуальной машины и установки клиента Chef.

## Дополнительные ресурсы

[Chef и Microsoft Azure][Chef и Microsoft Azure]

[Как войти в виртуальную машину под управлением Windows Server][Как войти в виртуальную машину под управлением Windows Server]

[Как войти в виртуальную машину под управлением Linux][Как войти в виртуальную машину под управлением Linux]

[Управление расширениями][Управление расширениями]

<!--Link references-->

  [Choose your installation]: http://www.getchef.com/chef/choose-your-version/
  [портале Microsoft Azure]: http://docs.opscode.com/azure_portal.html
  [скрипта]: https://gist.github.com/kaustubh-d/cea1aa75baebd3615609
  [knife-azure]: http://docs.getchef.com/plugin_knife_azure.html
  [Chef и Microsoft Azure]: http://www.getchef.com/solutions/azure/
  [Как войти в виртуальную машину под управлением Windows Server]: ../virtual-machines-log-on-windows-server/
  [Как войти в виртуальную машину под управлением Linux]: ../virtual-machines-linux-how-to-log-on
  [Управление расширениями]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
