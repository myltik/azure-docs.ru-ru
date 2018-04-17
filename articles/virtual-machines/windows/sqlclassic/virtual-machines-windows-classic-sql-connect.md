---
title: Подключение к виртуальной машине SQL Server (классическая модель) в Azure | Документация Майкрософт
description: Узнайте, как подключиться к системе SQL Server, выполняемой на виртуальной машине в Azure. В этом разделе используется классическая модель развертывания. Сценарии различаются в зависимости от конфигурации сети и расположения клиента.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: c856c8c67d410a3b528c4f8b12b1225cf395bca4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Подключение к виртуальной машине SQL Server в Azure (классическое развертывание)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Классический](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Обзор
В этом разделе показано, как подключиться к экземпляру SQL Server, выполняемому на виртуальной машине Azure. Сначала рассматриваются некоторые [общие сценарии подключения](#connection-scenarios), а затем предоставляются [подробные инструкции по настройке подключений SQL Server на виртуальной машине Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Если вы используете виртуальные машины Resource Manager, то ознакомьтесь со статьей [Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов)](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Сценарии подключения
Способ подключения клиента к системе SQL Server, выполняемой на виртуальной машине, зависит от расположения клиента, а также конфигурации компьютера и сети. Ниже приведены соответствующие сценарии.

* [Подключение к SQL Server в пределах одной облачной службы](#connect-to-sql-server-in-the-same-cloud-service)
* [Подключение к SQL Server через Интернет](#connect-to-sql-server-over-the-internet)
* [Подключение к SQL Server в пределах одной виртуальной сети](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Перед подключением посредством любого из этих методов необходимо выполнить [указания в этой статье, чтобы настроить возможность подключения](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Подключение к SQL Server в пределах одной облачной службы
В одной облачной службе можно создать несколько виртуальных машин. Этот сценарий использования виртуальных машин описан в статье [Как подключить виртуальные машины с помощью виртуальной сети или облачной службы](../classic/connect-vms-classic.md#connect-vms-in-a-standalone-cloud-service). В данном сценарии клиента на одной виртуальной машине пытается подключиться к SQL Server на другой виртуальной машине, расположенной в той же облачной службе.

В этом случае для подключения можно использовать **имя** виртуальной машины (которое также отображается как **имя компьютера** или **имя узла** на портале). Это имя, заданное при создании виртуальной машины. Например, если имя виртуальной машины SQL — **mysqlvm**, то клиентская виртуальная машины в той же облачной службе может использовать следующую строку подключения.

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Подключение к SQL Server через Интернет
Если вы хотите подключиться к ядру СУБД SQL Server из Интернета, необходимо создать конечную точку виртуальной машины для входящих подключений TCP. На данном этапе настройки Azure направляет входящий трафик TCP-порта на TCP-порт, доступный для виртуальной машины.

Для подключения через Интернет необходимо использовать DNS-имя и номер порта конечной точки виртуальной машины (их настройка описывается далее в этой статье). Чтобы найти DNS-имя, перейдите на портал Azure и щелкните **Виртуальные машины (классика)**. Затем выберите свою виртуальную машину. **DNS-имя** показано в разделе **Обзор**.

Например, рассмотрим классическую виртуальную машину **mysqlvm** с DNS-именем **mysqlvm7777.cloudapp.net** и конечной точкой **57500**. При условии, что подключение настроено правильно, для доступа к виртуальной машине из любого расположения в Интернете может использоваться следующая строка подключения.

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Хотя таким образом можно обеспечить подключение клиентов через Интернет, это не значит, что любой пользователь сможет подключиться к вашей системе SQL Server. Внешние клиенты должны иметь правильные имя пользователя и пароль. Чтобы повысить уровень безопасности, не используйте известный порт 1433 для общедоступной конечной точки виртуальной машины. Если возможно, рассмотрите вариант добавления списка ACL в конечной точке, чтобы сделать трафик доступным только для определенных клиентов. Инструкции по использованию списков ACL в конечных точках см. в разделе [Управление ACL для конечной точки](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Важно отметить, что при использовании этого метода для взаимодействия с SQL Server за все данные, исходящие из центра обработки данных Azure, взимается обычная [плата за исходящий трафик](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Подключение к SQL Server в пределах одной виртуальной сети
[Виртуальная сеть](../../../virtual-network/virtual-networks-overview.md) поддерживает дополнительные сценарии. Вы можете подключить виртуальные машины в одной виртуальной сети, даже если они расположены в разных облачных службах. Если используется [VPN типа «сеть-сеть»](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), можно создать гибридную архитектуру, обеспечивающую подключение виртуальных машин к локальным сетям и компьютерам.

Виртуальные сети также позволяют присоединить ваши виртуальные машины Azure к домену. Это единственный способ использовать проверку подлинности Windows в SQL Server. Другие сценарии подключения требуют проверки подлинности SQL с использованием имен пользователей и паролей.

Если вы собираетесь настроить среду домена и аутентификацию Windows, то не нужно выполнять описанные в этой статье действия по настройке общедоступных конечных точек, аутентификации SQL или имен для входа. В данном сценарии можно подключиться к экземпляру SQL Server, указав имя узла виртуальной машины SQL Server в строке подключения. В следующем примере предполагается, что также настроена проверка подлинности Windows, а пользователю предоставили доступ к экземпляру SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Действия по настройке подключения к SQL Server на виртуальной машине Azure
Ниже показано, как подключиться к экземпляру SQL Server через Интернет с помощью SQL Server Management Studio (SSMS). Однако, чтобы сделать виртуальную машину SQL Server доступной для приложений, выполняющихся как локально, так и в среде Azure, необходимо выполнить те же действия.

Чтобы подключиться к экземпляру SQL Server из другой виртуальной машины или через Интернет, необходимо выполнить задачи, описанные в следующих разделах:

* [Создание конечной точки TCP для виртуальной машины](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Открытие портов TCP в брандмауэре Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Настройка SQL Server для прослушивания через протокол TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Настройка SQL Server для аутентификации в смешанном режиме](#configure-sql-server-for-mixed-mode-authentication)
* [Создание имен входа для аутентификации SQL Server](#create-sql-server-authentication-logins)
* [Определение DNS-имени виртуальной машины](#determine-the-dns-name-of-the-virtual-machine)
* [Подключение к ядру СУБД с другого компьютера](#connect-to-the-database-engine-from-another-computer)

Процедура подключения показана на следующей схеме:

![Подключение к виртуальной машине SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Дальнейшие действия
Если вы также планируете использовать группы доступности AlwaysOn для обеспечения высокого уровня доступности и аварийного восстановления, следует реализовать прослушиватель. Клиенты баз данных подключаются к прослушивателю, а не непосредственно к одному из экземпляров SQL Server. Прослушиватель направляет клиентов к первичной реплике в группе доступности. Дополнительные сведения см. в статье [Настройка прослушивателя внутренней подсистемы балансировки нагрузки для группы доступности AlwaysOn в Azure](../classic/ps-sql-int-listener.md).

Важно просмотреть все рекомендации по обеспечению безопасности для системы SQL Server, выполняемой на виртуальной машине Azure. Дополнительные сведения см. в статье [Вопросы безопасности SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-security.md).

[Ознакомьтесь со схемой обучения](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) по использованию SQL Server в виртуальных машинах Azure. 

Другие темы, связанные с запуском SQL Server на виртуальных машинах Azure, рассматриваются в статье [SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

