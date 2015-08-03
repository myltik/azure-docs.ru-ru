<properties 
   pageTitle="Задание параметров DNS в файле конфигурации виртуальной сети"
   description="Описание"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/28/2015"
   ms.author="joaoma" />

# Задание параметров DNS в файле конфигурации виртуальной сети

Файл конфигурации сети содержит два элемента, позволяющих задавать параметры службы доменных имен (DNS): **DnsServers** и **DnsServerRef**. Список DNS-серверов можно добавить, задав их IP-адреса и ссылочные имена в элементе **DnsServers**. После этого в элементе **DnsServerRef** можно указать записи DNS-серверов из элемента DnsServers, которые будут применяться для различных сетевых сайтов в виртуальной сети.

>[AZURE.IMPORTANT]Дополнительную информацию о том, как настроить файл конфигурации сети, см. в разделе [Настройка виртуальной сети с помощью файлов конфигурации сети](https://msdn.microsoft.com/library/azure/jj156097.aspx). Информацию о каждом элементе, содержащемся в файле конфигурации сети, см. в разделе [Схема конфигурации виртуальной сети Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Файл конфигурации сети может содержать следующие элементы. Заголовок каждого элемента связан со страницей, содержащей дополнительную информацию о параметрах значений элемента.

[Элемент DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING]Атрибут **name** в элементе **DnsServer** используется только в качестве ссылки для элемента **DnsServerRef**. Он не представляет имя узла для DNS-сервера. Каждое значение атрибута **DnsServer** должно быть уникальным во всей подписке Microsoft Azure.

[Элемент Virtual Network Sites](http://go.microsoft.com/fwlink/?LinkId=248093)

	<DnsServersRef>
	  <DnsServerRef name="ID1" />
	  <DnsServerRef name="ID2" />
	  <DnsServerRef name="ID3" />
	</DnsServersRef>

>[AZURE.NOTE]Чтобы задать этот параметр для элемента Virtual Network Sites, его необходимо определить ранее в элементе DNS. *Имя* DnsServerRef в элементе Virtual Network Sites должно ссылаться на значение имени, указанное в элементе DNS для *имени* DnsServer.

## См. также

[Настройка виртуальной сети с помощью файлов конфигурации сети](http://go.microsoft.com/fwlink/?LinkId=248094)

[Схема настройки виртуальной сети Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Схема конфигурации службы Azure](https://msdn.microsoft.com/library/windowsazure/ee758710)

<!---HONumber=July15_HO4-->