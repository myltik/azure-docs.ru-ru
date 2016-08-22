.<properties 
   pageTitle="Настройка параметров DNS в файле конфигурации службы | Microsoft Azure"
   description="Настройка пользовательских параметров DNS с помощью файла конфигурации службы для виртуальной сети"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="jdial" />

# Настройка параметров DNS в файле конфигурации службы

## Элементы DNS

Файл конфигурации службы может содержать элемент DnsServers со списком IPv4-адресов для серверов доменных имен (DNS-серверов), которые будет использовать служба. Параметры файла конфигурации службы имеют приоритет над параметрами файла конфигурации сети. Дополнительную информацию см. в разделе [Схема конфигурации службы Azure (файл CSCFG)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Элемент NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] Атрибут **name** в элементе **DnsServer** используется только в качестве имени ссылки. Он не представляет имя узла для DNS-сервера. Каждое значение атрибута **DnsServer** должно быть уникальным во всей подписке Microsoft Azure.

## См. также

[Схема конфигурации службы Azure (CSCFG-файл)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Схема настройки виртуальной сети Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Настройка виртуальной сети с помощью файлов конфигурации сети](http://go.microsoft.com/fwlink/?LinkId=248094)

[Параметры виртуальной сети на портале управления](http://go.microsoft.com/fwlink/?LinkId=248092)

<!---HONumber=AcomDC_0810_2016-->