---
title: Создание виртуальной машины SQL Server 2017 на базе Linux в Azure | Документация Майкрософт
description: Это руководство содержит сведения о создании виртуальной машины SQL Server 2017 на базе Linux на портале Azure.
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 05/11/2018
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: b86dd47c112c38bc65c045158787d19b470899a0
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071729"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Подготовка виртуальной машины SQL Server на базе Linux на портале Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

В этом кратком руководстве вы будете использовать портал Azure для создания виртуальной машины Linux с установленным SQL Server 2017.

Изучив данный учебник, вы научитесь:

* [создавать виртуальную машину SQL на базе Linux из коллекции](#create);
* [подключаться к новой виртуальной машине по протоколу SSH](#connect);
* [изменять пароль системного администратора](#password);
* [настраивать удаленные подключения](#remote).

## <a name="prerequisites"></a>предварительным требованиям

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a id="create"></a> Создание виртуальной машины Linux с установленным SQL Server

1. Войдите на [портале Azure](https://portal.azure.com/).

1. В области слева щелкните **Создать ресурс**.

1. В области **Создать ресурс** щелкните **Вычисления**.

1. Щелкните **Просмотреть все** рядом с заголовком **Избранные**.

   ![Просмотр всех образов виртуальных машин](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. В поле поиска введите **SQL Server 2017** и нажмите клавишу **ВВОД**, чтобы начать поиск.

1. Щелкните значок **Фильтр**, сузьте поиск образов, задав критерии **На основе Linux** и **Microsoft**, а затем нажмите кнопку **Готово**.

    ![Фильтр поиска для образов виртуальной машины SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Выберите образ SQL Server 2017 на базе Linux в результатах поиска. В этом руководстве используется **бесплатная лицензия SQL Server: SQL Server 2017 Developer в Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > Developer Edition позволяет выполнять тестирование или разработку, используя функции Enterprise Edition без затрат на лицензию SQL Server. Вы оплачиваете только стоимость выполнения виртуальной машины Linux.

1. Нажмите кнопку **Создать**.

1. В окне **Основы** укажите сведения для виртуальной машины Linux. 

    ![Окно "Основы"](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Для проверки подлинности вы можете использовать открытый ключ или пароль SSH. SSH является более безопасным. Сведения о создании ключа SSH см. в статье [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Последовательно выберите **ОК**.

1. В окне **Размер** выберите размер машины. Дополнительные сведения о размерах виртуальных машин см. в [этой статье](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Выбор размера виртуальной машины](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Для разработки и функционального тестирования мы рекомендуем использовать размер виртуальной машины **DS2** или выше. Для тестирования производительности используйте **DS13** или выше.

1. Нажмите кнопку **Выбрать**.

1. В окне **параметры** выберите порт **SSH (22)** в списке **общедоступных входящих портов**. В этом руководстве это необходимо для подключения и завершения настройки SQL Server. Если вы хотите удаленно подключиться к SQL Server, также выберите **MS SQL (1433)**, чтобы открыть порт 1433 для подключения через Интернет.

   ![Входящие порты](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Вы можете изменить параметры или оставить параметры по умолчанию. Нажмите кнопку **ОК**.

1. На странице **Сводка** щелкните **Приобрести** для создания виртуальной машины.

## <a id="connect"></a> Подключение к виртуальной машине Linux

Если вы уже используете оболочку BASH, подключитесь к виртуальной машине Azure с помощью команды **SSH**. В следующей команде замените имя пользователя и IP-адрес виртуальной машины, чтобы подключиться к виртуальной машине Linux.

```bash
ssh azureadmin@40.55.55.555
```

IP-адрес виртуальной машины можно найти на портале Azure.

![IP-адрес на портале Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Если вы используете Windows и у вас нет оболочки BASH, можно установить клиент SSH, например PuTTY.

1. [Скачайте и установите PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Запустите PuTTY.

1. На экране настройки PuTTY введите общедоступный IP-адрес виртуальной машины.

1. Выберите "Открыть" и введите имя пользователя и пароль при появлении запросов.

Дополнительные сведения о подключении к виртуальным машинам Linux см. в статье [Создание виртуальной машины Linux с помощью портала Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a> Изменение пароля системного администратора

Новая виртуальная машина устанавливает SQL Server со случайным паролем системного администратора. Вам необходимо сбросить этот пароль, прежде чем подключиться к SQL Server с помощью имени для входа системного администратора.

1. После подключения к виртуальной машине Linux откройте новое окно терминала команд.

1. Измените пароль системного администратора, выполнив следующие команды:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   При появлении запроса введите новый пароль системного администратора и повторите его еще раз для подтверждения.

1. Перезапустите службу SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Добавление средств в путь (необязательно)

Несколько [пакетов](sql-server-linux-virtual-machines-overview.md#packages) SQL Server установлены по умолчанию, включая пакет программ командной строки SQL Server. Пакет средств содержит средства **sqlcmd** и **bcp**. Для удобства можно по желанию добавить путь к средствам (`/opt/mssql-tools/bin/`) в переменную среды **PATH**.

1. Выполните следующие команды, чтобы изменить **PATH** для обоих сеансов входа и интерактивных сеансов или сеансов без входа.

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Настройка удаленных подключений

Если необходимо выполнить удаленное подключение к SQL Server на виртуальной машине Azure, необходимо настроить правило входящих подключений в группе безопасности сети. Это правило разрешает передачу трафика на порте, на котором SQL Server ожидает передачу данных (по умолчанию 1433). Ниже показано, как использовать портал Azure для выполнения этого шага.

> [!TIP]
> Если вы выбрали входящий порт **MS SQL (1433)** в параметрах во время подготовки, эти изменения были сделаны автоматически. Затем можно перейти к следующему разделу, в котором описано, как настроить брандмауэр

1. На портале выберите **Виртуальные машины**, а затем виртуальную машину сервера SQL.

1. В списке свойств выберите **Сети**.

1. В окне **Сети** в разделе **Inbound Port Rules** (Правила для входящих портов) нажмите кнопку **Добавить**.

   ![Правила для входящих портов](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. В списке **Служба** выберите **MS SQL**.

    ![Правило группы безопасности MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Нажмите кнопку **ОК** , чтобы сохранить правило для виртуальной машины.

### <a name="open-the-firewall-on-rhel"></a>Открытие брандмауэра в RHEL

Это руководство содержит сведения о создании виртуальной машины Red Hat Enterprise Linux (RHEL). Если вы хотите удаленно подключаться к виртуальным машинам RHEL, необходимо открыть порт 1433 в брандмауэре Linux.

1. [Подключитесь](#connect) к виртуальной машине RHEL.

1. В оболочке BASH выполните следующие команды:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда у вас есть виртуальная машина SQL Server 2017 в Azure, можно локально подключиться с помощью **sqlcmd** для выполнения запросов Transact-SQL.

Если вы настроили виртуальную машину Azure для удаленных подключений SQL Server, вы также сможете выполнить удаленное подключение. Пример того, как выполнить удаленное подключение к SQL Server на базе Linux из Windows, см. в статье [Use SQL Server Management Studio (SSMS) on Windows to manage SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) (Использование SQL Server Management Studio (SSMS) в Windows для управления SQL Server в Linux). Сведения о подключении с помощью Visual Studio Code см. в статье [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) (Использование Visual Studio Code для создания и выполнения скриптов Transact-SQL для SQL Server).

Дополнительные сведения об SQL Server на базе Linux см. в [этой статье](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Подробные сведения об использовании виртуальных машин SQL Server 2017 на базе Linux см. в статье [Обзор SQL Server на виртуальных машинах Azure (Linux)](sql-server-linux-virtual-machines-overview.md).
