---
title: Пользовательская служба DNS для Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: В этой статье описаны параметры конфигурации пользовательской службы DNS для Управляемого экземпляра Базы данных SQL Azure.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 05a7b600ae8672447126b79cda10ca94c6d0fb48
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649335"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Настройка пользовательской службы DNS для Управляемого экземпляра Базы данных SQL Azure

Управляемый экземпляр Базы данных SQL Azure (предварительная версия) нужно развернуть в [виртуальной сети (VNet)](../virtual-network/virtual-networks-overview.md) Azure. Иногда для связанных серверов других экземпляров SQL в облачной или гибридной среде требуется, чтобы имена частных узлов разрешались из Управляемого экземпляра. В таком случае в Azure необходимо настроить пользовательскую службу DNS. Так как для внутренней работы в Управляемом экземпляре используется одна служба DNS, конфигурация DNS виртуальной сети должна быть совместима с Управляемым экземпляром. 

Чтобы обеспечить совместимость конфигурации пользовательской службы DNS с Управляемым экземпляром, выполните следующие действия: 
- Настройте пользовательскую службу DNS для перенаправления запросов Azure DNS. 
- Настройте пользовательскую службу DNS в качестве основной службы и Azure DNS в качестве дополнительной службы для виртуальной сети. 
- Зарегистрируйте пользовательскую службу DNS в качестве основной службы и Azure DNS в качестве дополнительной службы.

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Настройка пользовательской службы DNS для перенаправления запросов Azure DNS 

Чтобы настроить переадресацию DNS в ОС Windows Server 2016, выполните следующие действия: 

1. В **диспетчере сервера** щелкните **Средства** и выберите **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Дважды щелкните **Серверы пересылки**.

   ![Серверы пересылки](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Нажмите кнопку **Изменить**. 

   ![Список серверов пересылки](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Введите IP-адрес рекурсивных сопоставителей Azure, например 168.63.129.16.

   ![IP-адрес рекурсивных сопоставителей](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Настройка пользовательской службы DNS в качестве основной службы и Azure DNS в качестве дополнительной 
 
Чтобы настроить DNS для виртуальной сети Azure, необходимо ввести IP-адреса. Выполните приведенные ниже инструкции, чтобы настроить виртуальную машину Azure, на которой размещается DNS-сервер со статическим IP-адресом. 

1. На портале Azure откройте пользовательский сетевой интерфейс виртуальной машины DNS.

   ![сетевой интерфейс](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. В разделе конфигураций IP выберите элемент "Конфигурация IP". 

   ![конфигурация IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Задайте для частного IP-адреса значение "Статический". Запишите IP-адрес (10.0.1.5 на этом снимке экрана). 

   ![static](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Регистрация пользовательской службы DNS в качестве основной службы и Azure DNS в качестве дополнительной 

1. На портале Azure найдите параметр "Пользовательская служба DNS" для своей виртуальной сети.

   ![Параметр "Пользовательская служба DNS"](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Установите переключатель в положение Custom (Пользовательская) и введите пользовательский IP-адрес сервера DNS, а также IP-адрес рекурсивных сопоставителей Azure, например 168.63.129.16. 

   ![Параметр "Пользовательская служба DNS"](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Если не указать в списке DNS рекурсивный сопоставитель Azure, Управляемый экземпляр перейдет в состояние сбоя. Чтобы восстановить решение из этого состояния, возможно, в виртуальной сети потребуется создать новый экземпляр с соответствующими политиками сети и данные уровня экземпляра, а также восстановить базы данных. См. статью о [конфигурации виртуальной сети](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Общие сведения об Управляемом экземпляре Базы данных SQL Azure (предварительная версия)](sql-database-managed-instance.md).
- См. дополнительные сведения о [создании Управляемого экземпляра](sql-database-managed-instance-create-tutorial-portal.md).
- См. дополнительные сведения о [настройке виртуальной сети для Управляемого экземпляра](sql-database-managed-instance-vnet-configuration.md).
