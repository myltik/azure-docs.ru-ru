<properties
	pageTitle="Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов) | Microsoft Azure"
	description="Узнайте, как подключиться к системе SQL Server, выполняемой на виртуальной машине в Azure. В этом разделе используется классическая модель развертывания. Сценарии различаются в зависимости от конфигурации сети и расположения клиента."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"    
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/21/2016"
	ms.author="jroth" />

# Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-sql-connect.md)
- [Классический](virtual-machines-windows-classic-sql-connect.md)

## Обзор

В этом разделе показано, как подключиться к экземпляру SQL Server, выполняемому на виртуальной машине Azure. Сначала рассматриваются некоторые [общие сценарии подключения](#connection-scenarios), а затем предоставляются [подробные инструкции по настройке подключений SQL Server на виртуальной машине Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]  
Классическая модель развертывания. Версию этой статьи для классической модели развертывания см. в разделе [Подключение к виртуальной машине SQL Server в Azure (классическое развертывание)](virtual-machines-windows-classic-sql-connect.md).

Если вы предпочитаете полное пошаговое руководство по подготовке и подключению, то см. статью [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

## Сценарии подключения

Способ подключения клиента к системе SQL Server, выполняемой на виртуальной машине, зависит от расположения клиента, а также конфигурации компьютера и сети. Ниже приведены соответствующие сценарии.

- [Подключение к SQL Server через Интернет](#connect-to-sql-server-over-the-internet)
- [Подключение к SQL Server в пределах одной виртуальной сети](#connect-to-sql-server-in-the-same-virtual-network)

### Подключение к SQL Server через Интернет

Если вы хотите подключиться к СУБД SQL Server из Интернета, нужно выполнить некоторые необходимые действия, такие как настройка брандмауэра, включение проверки подлинности SQL и настройка группы безопасности вашей сети. У вас должно быть настроено правило группы безопасности сети, которое разрешает трафик TCP на порт 1433.

Если вы используете портал для подготовки образа виртуальной машины SQL Server с помощью Resource Manager, то следующие действия будут выполнены автоматически при выборе варианта подключения SQL **Общедоступное**:

![Общедоступное подключение SQL во время подготовки](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Если это не было сделано во время подготовки, то можно настроить SQL Server и виртуальные машины вручную, выполнив [действия по настройке подключения вручную, описанные в этой статье](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] Образ виртуальной машины выпуска SQL Server Express не включает протокол TCP/IP автоматически, Для выпуска Express необходимо [включить протокол TCP/IP вручную](#configure-sql-server-to-listen-on-the-tcp-protocol) в диспетчере конфигурации SQL Server после создания виртуальной машины.

После этого любой клиент с доступом к Интернету может подключиться к экземпляру SQL Server, указав общедоступный IP-адрес виртуальной машины или имя DNS, назначенное этому IP-адресу. Если порт SQL Server — 1433, необходимо указать его в строке подключения.

	"Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Хотя таким образом можно обеспечить подключение клиентов через Интернет, это не значит, что любой пользователь сможет подключиться к вашей системе SQL Server. Внешние клиенты должны иметь правильные имя пользователя и пароль. Для обеспечения дополнительной безопасности можно использовать другой порт вместо хорошо известного 1433. Например, если настроить SQL Server на прослушивание порта 1500 и установить соответствующие правила брандмауэра группы безопасности сети, то для подключения нужно будет добавить номер порта к имени сервера, как показано в следующем примере:

	"Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] Важно отметить, что при использовании этого метода для взаимодействия с SQL Server за все данные, исходящие из центра обработки данных Azure, взимается обычная [плата за исходящий трафик](https://azure.microsoft.com/pricing/details/data-transfers/).

### Подключение к SQL Server в пределах одной виртуальной сети

[Виртуальная сеть](../virtual-network/virtual-networks-overview.md) поддерживает дополнительные сценарии. Вы можете подключить виртуальные машины в одной виртуальной сети, даже если они расположены в разных группах ресурсов. Если используется [VPN типа «сеть-сеть»](../vpn-gateway/vpn-gateway-site-to-site-create.md), можно создать гибридную архитектуру, обеспечивающую подключение виртуальных машин к локальным сетям и компьютерам.

Виртуальные сети также позволяют присоединить ваши виртуальные машины Azure к домену. Это единственный способ использовать проверку подлинности Windows в SQL Server. Другие сценарии подключения требуют проверки подлинности SQL с использованием имен пользователей и паролей.

Если вы используете портал для подготовки образа виртуальной машины SQL Server с помощью Resource Manager, надлежащие правила брандмауэра для обмена данными в виртуальной сети будут настроены при выборе варианта подключения SQL **Частное**. Если это не было сделано во время подготовки, можно настроить SQL Server и виртуальные машины вручную, выполнив [действия по настройке подключения вручную, описанные в этой статье](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Однако если вы собираетесь настроить доменную среду и проверку подлинности Windows, не нужно выполнять описанные в этой статье действия по настройке проверки подлинности SQL и входа в систему. Также не нужно настраивать правила групп безопасности сети для доступа через Интернет.

>[AZURE.NOTE] Образ виртуальной машины выпуска SQL Server Express не включает протокол TCP/IP автоматически, Для выпуска Express необходимо [включить протокол TCP/IP вручную](#configure-sql-server-to-listen-on-the-tcp-protocol) в диспетчере конфигурации SQL Server после создания виртуальной машины.

Если вы настроили DNS в своей виртуальной сети, то сможете подключиться к экземпляру SQL Server, указав имя узла виртуальной машины SQL Server в строке подключения. В следующем примере также предполагается, что настроена проверка подлинности Windows, а пользователю предоставили доступ к экземпляру SQL Server.

	"Server=mysqlvm;Integrated Security=true"

Обратите внимание, что в этом сценарии также можно указать IP-адрес виртуальной машины.

## Действия по ручной настройке подключения к SQL Server на виртуальной машине Azure

Ниже показано, как вручную настроить подключение к экземпляру SQL Server и затем при необходимости подключиться через Интернет с помощью SQL Server Management Studio (SSMS). Обратите внимание, что многие из этих действий выполняются автоматически при выборе соответствующего варианта подключения к SQL Server на портале.

Чтобы подключиться к экземпляру SQL Server из другой виртуальной машины или через Интернет, необходимо выполнить задачи, описанные в следующих разделах:

- [Открытие портов TCP в брандмауэре Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Настройка SQL Server для прослушивания через протокол TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Настройка SQL Server для аутентификации в смешанном режиме](#configure-sql-server-for-mixed-mode-authentication)
- [Создание имен входа для аутентификации SQL Server](#create-sql-server-authentication-logins)
- [Настройка правила для входящего трафика группы безопасности сети](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Настройка имени DNS для общедоступного IP-адреса](#configure-a-dns-label-for-the-public-ip-address)
- [Подключение к ядру СУБД с другого компьютера](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине в диспетчере ресурсов](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине в диспетчере ресурсов](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## Дальнейшие действия

Инструкции по подготовке и действия по подключению приведены в статье [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Ознакомьтесь со схемой обучения](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) по использованию SQL Server в виртуальных машинах Azure.

Другие темы, связанные с запуском SQL Server на виртуальных машинах Azure, рассматриваются в статье [SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0921_2016-->