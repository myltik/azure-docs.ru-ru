<properties 
   pageTitle="Использование динамических DNS для регистрации имен узлов"
   description="На этой странице приведены некоторые сведения настройке динамических DNS для регистрации имен узлов на DNS-серверах."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Использование динамических DNS для регистрации имен узлов
[Azure предоставляет разрешение имен](virtual-networks-name-resolution-for-vms-and-role-instances.md) для виртуальных машин и экземпляров ролей. Если разрешение имен должно выходить за рамки предоставленного разрешения Azure, вы можете предоставить свои DNS-серверы. В этом случае ваше решение DNS можно настроить в соответствии с конкретными потребностями. Например, для доступа к локальному контроллеру домена AD.

Azure не может (не имеет учетных данных) регистрировать записи непосредственно на DNS-серверах, поэтому часто требуется прибегать к альтернативным вариантам. Ниже приведены общие сведения о некоторых наиболее распространенных сценариях.

## Клиенты Windows ##
Не входящие в домен клиенты Windows пытаются выполнить небезопасные обновления DDNS при загрузке или изменении их IP-адреса. DNS-именем является имя узла и основной DNS-суффикс. Azure оставляет основной DNS-суффикс пустым, но это действие может быть переопределено на виртуальной машине с помощью [пользовательского интерфейса](https://technet.microsoft.com/library/cc794784.aspx) или [автоматизации](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Входящие в домен клиенты Windows регистрируют свои IP-адреса на контроллере домена с помощью Secure DDNS. Процесс присоединения к домену задает основной DNS-суффикс на стороне клиента и управляет отношением доверия.

## Клиенты Linux ##
Клиенты Linux обычно не регистрируются на DNS-сервере при запуске — они предполагают, что это делает DHCP-сервер. Пакет привязки входит в состав средства *nsupdate*, которое можно использовать для отправки обновлений DDNS. Так как DDNS-протокол является стандартизированным, средство *nsupdate* можно применять даже в том случае, если привязка не используется на DNS-сервере.

DHCP-клиент Linux предоставляет обработчики, которые позволяют запускать сценарии при назначении или изменении IP-адреса. Например, в */etc/dhcp/dhclient-exit-hooks.d/*. Это можно использовать для регистрации имени узла в DNS. Например:
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

Команда *Nsupdate* может также выполнять безопасные обновления DDNS. Например, при использовании привязки DNS-сервера [создается](http://linux.yyz.us/nsupdate/) пара открытого и закрытого ключей и DNS-сервер [настраивается](http://linux.yyz.us/dns/ddns-server.html) с открытой частью ключа, поэтому он может проверить подпись запроса. Чтобы подписать запрос на обновление DDNS, в *nsupdate* необходимо указать пару ключей с помощью параметра *-k*.

При использовании DNS-сервера Windows можно выполнять проверку подлинности Kerberos с параметром *-g* nsupdate (недоступно в *nsupdate* версии Windows). Для этого используйте *kinit*, чтобы скачать учетные данные (например из [файла keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), а затем *nsupdate -g* получит учетные данные из кэша.

При необходимости в виртуальные машины можно добавить суффикс поиска DNS. DNS-суффикс указан в файле */etc/resolv.conf*. Большинство дистрибутивов Linux управляет содержимым этого файла автоматически, поэтому его обычно нельзя изменить. Однако суффикс можно переопределить с помощью команды *supersede* DHCP-клиента — в */etc/dhcp/dhclient.conf* добавьте:

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->