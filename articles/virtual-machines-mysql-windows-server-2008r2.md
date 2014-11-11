<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav"></tags>

# Установка MySQL на виртуальной машине под управлением Windows Server 2008 R2 в Azure

[MySQL][MySQL] является популярной базой данных SQL с открытым исходным кодом. С помощью [портала управления Azure][портала управления Azure] можно создать виртуальную машину под управлением Windows Server 2008 R2 из коллекции образов. Затем можно установить и настроить базу данных MySQL на виртуальной машине.

Из этого учебника вы узнаете следующее:

-   Создание виртуальной машины под управлением Windows Server 2008 R2 с помощью портала управления.

-   Установка и запуск MySQL Community Server на виртуальной машине.

## Создание виртуальной машины под управлением ОС Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

## Присоединение диска данных

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

## Установка и запуск MySQL Community Server на виртуальной машине

Для установки, настройки и запуска MySQL Community Server выполните следующие действия:

1.  После подключения к виртуальной машине с помощью удаленного рабочего стола откройте **Internet Explorer** из меню **Пуск**.

2.  Нажмите кнопку **Сервис** в правом верхнем углу. В окне **Свойства браузера** выберите вкладку **Безопасность**, выберите значок **Надежные сайты**, а затем нажмите кнопку **Сайты**. Добавьте *http://\*.mysql.com* в список надежных сайтов.

3.  Перейдите на страницу [Download MySQL Community Server][Download MySQL Community Server].

4.  Выберите **Microsoft Windows** в раскрывающемся меню **Платформа** и щелкните **Выбрать**.

5.  Найдите самый последний выпуск **Windows (x86, 64-разрядная), установщик MSI** и нажмите кнопку **Скачать**.

6.  Выберите **Нет, просто начать загрузку!** (или зарегистрируйте учетную запись). Если будет предложено, выберите сайт зеркальной загрузки установщика MySQL и сохраните установщик на рабочем столе.

7.  Дважды щелкните файл установщика на рабочем столе, чтобы начать установку.

8.  Нажмите кнопку **Далее**.

    ![Установка MySQL][Установка MySQL]

9.  Примите условия лицензионного соглашения и нажмите кнопку **Далее**.

    ![Установка MySQL][1]

10. Нажмите кнопку **Обычная** для установки общих компонентов.

    ![Установка MySQL][2]

11. Нажмите **Install** (Установить).

    ![Установка MySQL][3]

12. Запустите мастер настройки MySQL и щелкните **Далее**.

    ![Настройка MySQL][Настройка MySQL]

13. Выберите **Подробные настройки** и щелкните «Далее».

    ![Настройка MySQL][4]

14. Выберите **Сервер**, если вы планируете запустить MySQL вместе с другими приложениями на сервере, или выберите вариант, который наилучшим образом соответствует вашим потребностям. Нажмите кнопку **Далее**.

    ![Настройка MySQL][5]

15. Выберите **Многофункциональная база данных** или вариант, который наилучшим образом соответствует вашим потребностям. Нажмите кнопку **Далее**.

    ![Настройка MySQL][6]

16. Выберите диск с данными, подсоединенный ранее.

    ![Настройка MySQL][7]

17. Выберите **Поддержка принятия решений (DSS)/OLAP** или вариант, который наилучшим образом соответствует вашим потребностям. Нажмите кнопку **Далее**.

    ![Настройка MySQL][8]

18. Выберите **Включение сети TCP/IP** и **Добавить исключения брандмауэра для порта** (это создаст правила входящего трафика в брандмауэре Windows с именем **сервер MySQL**).

    ![Настройка MySQL][9]

19. Выберите **Максимальная поддержка различных языков**, если необходимо хранить текст на различных языках, или выберите вариант, который наилучшим образом соответствует вашим потребностям. Нажмите кнопку **Далее**.

    ![Настройка MySQL][10]

20. Выберите **Установить как службу Windows** и **Автоматически запустить сервер MySQL**. Кроме того, выберите **Включить каталог Bin в Windows PATH**. Нажмите кнопку **Далее**.

    ![Настройка MySQL][11]

21. Введите корневой пароль. Не отмечайте пункты **Разрешить доступ с правами корневого пользователя с удаленных компьютеров** или **Создать анонимную учетную запись**. Нажмите кнопку **Далее**.

    ![Настройка MySQL][12]

22. Щелкните **Выполнить** и дождитесь завершения настройки.

    ![Настройка MySQL][13]

23. Нажмите кнопку **Готово**

    ![Настройка MySQL][14]

24. Щелкните **Пуск** и выберите **Клиент командной строки MySQL 5.x**, чтобы запустить клиент командной строки.

25. При появлении запроса введите корневой пароль MySQL (который был установлен на предыдущем шаге), чтобы открыть командное окно, в котором можно вводить инструкции SQL для взаимодействия с базой данных.

26. Чтобы создать нового пользователя MySQL, выполните следующую команду в командной строке **mysql\>**:

        mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Обратите внимание, что точка с запятой в конце строки является символом завершения команды.

27. Чтобы создать базу данных и предоставить `mysqluser` разрешения пользователю, выполните следующие команды:

        mysql> CREATE DATABASE testdatabase;
        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Обратите внимание, что имена пользователей и пароли для базы данных используются только скриптами, подключающимися к базе данных. Имена учетных записей базы данных могут не совпадать с учетными записями компьютера.

28. Чтобы войти с другого компьютера, выполите следующую команду:

        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

    где `ip-address` — это IP-адрес компьютера, с которого осуществляется подключение к MySQL.

29. Чтобы выйти из клиента командной строки MySQL, выполните следующую команду:

        quit

30. После установки MySQL необходимо настроить конечную точку для удаленного доступа к MySQL. Выполните вход на [портал управления Azure][портала управления Azure]. На портале Azure щелкните **Виртуальные машины** и выберите имя новой виртуальной машины, а затем щелкните **Конечные точки** и **Добавить конечную точку**.

    ![Конечные точки][Конечные точки]

31. Выберите **Добавить конечную точку** и щелкните стрелку для продолжения.

    ![Конечные точки][15]

32. Добавьте конечную точку с именем "MySQL", протоколом **TCP** и портами **Общий** и **Частный** со значением "3306". Щелкните значок галочки. Это позволит получать удаленный доступ к MySQL.

    ![Конечные точки][16]

33. Можно удаленно подключиться к MySQL на виртуальной машине в Azure. На локальном компьютере под управлением MySQL выполните следующую команду для входа в систему в качестве пользователя **mysqluser**, созданного на предыдущих этапах:

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    Например, при использовании виртуальной машины, созданной в этом руководстве, команда должна быть такой:

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

## Сводка

В этом учебнике вы научились создавать виртуальную машину Windows 2008 R2 и удаленно подключаться к ней. Было также показано, как установить и настроить MySQL на виртуальной машине, создать базу данных и нового пользователя MySQL. Дополнительные сведения о MySQL см. в [документации по MySQL][документации по MySQL].

  [MySQL]: http://www.mysql.com
  [портала управления Azure]: http://manage.windowsazure.com
  [create-and-configure-windows-server-2008-vm-in-portal]: ../includes/create-and-configure-windows-server-2008-vm-in-portal.md
  [attach-data-disk-windows-server-2008-vm-in-portal]: ../includes/attach-data-disk-windows-server-2008-vm-in-portal.md
  [Download MySQL Community Server]: http://www.mysql.com/downloads/mysql/
  [Установка MySQL]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
  [1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
  [2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
  [3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
  [Настройка MySQL]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
  [4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
  [5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
  [6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
  [7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
  [8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
  [9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
  [10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
  [11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
  [12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
  [13]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
  [14]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
  [Конечные точки]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
  [15]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
  [16]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
  [документации по MySQL]: http://dev.mysql.com/doc/
