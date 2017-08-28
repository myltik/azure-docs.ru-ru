---
title: "Подключение к виртуальной машине SQL Server в Azure (Resource Manager) | Документация Майкрософт"
description: "Узнайте, как подключиться к системе SQL Server, выполняемой на виртуальной машине в Azure. В этом разделе используется классическая модель развертывания. Сценарии различаются в зависимости от конфигурации сети и расположения клиента."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/14/2017
ms.author: jroth
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 67ba43f9456bbeffbf602067586143c4c68af672
ms.contentlocale: ru-ru
ms.lasthandoff: 08/15/2017

---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов)
> [!div class="op_single_selector"]
> * [Диспетчер ресурсов](virtual-machines-windows-sql-connect.md)
> * [Классический](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Обзор

В этом разделе показано, как подключиться к экземпляру SQL Server, выполняемому на виртуальной машине Azure. Сначала рассматриваются некоторые [общие сценарии подключения](#connection-scenarios), а затем предоставляются [подробные инструкции по настройке подключений SQL Server на виртуальной машине Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Если вы предпочитаете полное пошаговое руководство по подготовке и подключению, то см. статью [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Сценарии подключения

Способ подключения клиента к системе SQL Server, выполняемой на виртуальной машине, зависит от расположения клиента и конфигурации сети.

При подготовке виртуальной машины SQL Server на портале Azure вы можете указать тип **подключения SQL**.

![Общедоступное подключение SQL во время подготовки](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Варианты подключения:

| Параметр | Описание |
|---|---|
| **Общедоступное** | Подключение к SQL Server через Интернет |
| **Частное** | Подключение к SQL Server в пределах одной виртуальной сети |
| **Локальное** | Локальное подключение к SQL Server на одной и той же виртуальной машине | 

В следующих разделах более подробно описаны типы подключения **Общедоступное** и **Частное**.

## <a name="connect-to-sql-server-over-the-internet"></a>Подключение к SQL Server через Интернет

Если вы хотите подключиться к ядру СУБД SQL Server из Интернета, на портале во время подготовки выберите для **подключения SQL** вариант **Общедоступное**. Действия, автоматически выполняемые порталом:

* включение протокола TCP/IP для SQL Server;
* настройка правила брандмауэра для открытия TCP-порта SQL Server (по умолчанию 1433);
* включение аутентификации SQL Server, требуемой для предоставления общего доступа;
* настройка группы безопасности сети на виртуальной машине для применения ко всему TCP-трафику, поступающему на порт SQL Server.

> [!IMPORTANT]
> Образ виртуальной машины для выпусков SQL Server Developer и Express не включает протокол TCP/IP автоматически. Для выпусков Developer и Express после создания виртуальной машины необходимо [вручную включить протокол TCP/IP](#manualtcp) в диспетчере конфигурации SQL Server.

После этого любой клиент с доступом к Интернету сможет подключиться к экземпляру SQL Server, указав общедоступный IP-адрес виртуальной машины или имя DNS, назначенное этому IP-адресу. Если порт SQL Server — 1433, необходимо указать его в строке подключения. Следующая строка подключения подключается к виртуальной машине SQL с меткой DNS `sqlvmlabel.eastus.cloudapp.azure.com` посредством аутентификации SQL (также можно использовать общедоступный IP-адрес).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Хотя таким образом можно обеспечить подключение клиентов через Интернет, это не значит, что любой пользователь сможет подключиться к вашей системе SQL Server. Внешние клиенты должны иметь правильные имя пользователя и пароль. Тем не менее для обеспечения дополнительной безопасности можно использовать вместо порта 1433 другой порт. Например, если настроить SQL Server на прослушивание порта 1500 и установить соответствующие правила брандмауэра и группы безопасности сети, для подключения нужно будет добавить к имени сервера номер порта. В примере ниже предыдущий порт изменяется после добавления номера настраиваемого порта (**1500**) к имени сервера:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> При обращении к SQL Server на виртуальной машине через Интернет, за все данные, поступающие из центра обработки данных Azure, взимается обычная [плата за исходящий трафик](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Подключение к SQL Server в пределах одной виртуальной сети

Если на портале вы выберете для **подключения SQL** вариант **Частное**, Azure настроит большинство параметров так же, как и для варианта **Общедоступное**. Единственное отличие заключается в том, что отсутствует правило группы безопасности сети для разрешения внешнего трафика через порт SQL Server (по умолчанию 1433).

> [!IMPORTANT]
> Образ виртуальной машины для выпусков SQL Server Developer и Express не включает протокол TCP/IP автоматически. Для выпусков Developer и Express после создания виртуальной машины необходимо [вручную включить протокол TCP/IP](#manualtcp) в диспетчере конфигурации SQL Server.

Частное подключение часто используется в сочетании с [виртуальной сетью](../../../virtual-network/virtual-networks-overview.md), что позволяет реализовать несколько сценариев. Вы можете подключить виртуальные машины в одной виртуальной сети, даже если они расположены в разных группах ресурсов. Если используется [VPN типа «сеть-сеть»](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), можно создать гибридную архитектуру, обеспечивающую подключение виртуальных машин к локальным сетям и компьютерам.

Виртуальные сети также позволяют присоединять виртуальные машины Azure к домену. Это единственный способ использовать проверку подлинности Windows в SQL Server. Другие сценарии подключения требуют проверки подлинности SQL с использованием имен пользователей и паролей.

Если вы настроили DNS в своей виртуальной сети, то сможете подключиться к экземпляру SQL Server, указав имя узла виртуальной машины SQL Server в строке подключения. В следующем примере также предполагается, что настроена проверка подлинности Windows, а пользователю предоставили доступ к экземпляру SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Изменение параметров подключения к SQL

Можно изменить параметры подключения для вашей виртуальной машины SQL Server на портале Azure.

1. На портале Azure выберите **Виртуальные машины**.

2. Выберите виртуальную машину SQL Server.

3. В разделе **Параметры** щелкните **Конфигурация SQL Server**.

4. Измените **уровень подключения SQL**, выбрав необходимый параметр. Эту область можно при необходимости использовать, чтобы изменить порт SQL Server или параметры аутентификации SQL.

   ![Изменение параметров подключения к SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Подождите несколько минут, пока установка обновления не будет завершена.

   ![Уведомление об обновлении виртуальной машины SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Включение TCP/IP для выпусков Developer и Express

При изменении параметров подключения к SQL Server Azure не включает протокол TCP/IP для выпусков SQL Server Developer и Express автоматически. Ниже описано, как вручную включить TCP/IP для удаленного подключения по IP-адресу.

Сначала подключитесь к виртуальной машине SQL Server через удаленный рабочий стол.

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Затем включите протокол TCP/IP в **диспетчере конфигурации SQL Server**.

> [!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Подключение с помощью SSMS

Ниже показано, как создать необязательную метку DNS для виртуальной машины Azure и подключиться с помощью SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Дальнейшие действия

Инструкции по подготовке и действия по подключению приведены в статье [Подготовка виртуальной машины SQL Server на портале Azure](virtual-machines-windows-portal-sql-server-provision.md).

Другие темы, связанные с запуском SQL Server на виртуальных машинах Azure, рассматриваются в статье [SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).
