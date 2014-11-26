<properties urlDisplayName="Install MySQL" pageTitle="Создание виртуальной машины с MySQL в Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav" />


#Установка MySQL на виртуальной машине под управлением Windows Server 2008 R2 в Azure

[MySQL](http://www.mysql.com) является популярной базой данных SQL с открытым исходным кодом. С помощью [портала управления Azure][AzurePreviewPortal], можно создать виртуальную машину под управлением Windows Server 2008 R2 из коллекции образов.  Затем можно установить и настроить базу данных MySQL на виртуальной машине.

Из этого учебника вы узнаете следующее:

- Создание виртуальной машины под управлением Windows Server 2008 R2 с помощью портала управления.

- Установка и запуск MySQL Community Server на виртуальной машине.

##Создание виртуальной машины под управлением ОС Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

##Присоединение диска данных

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

##Установка и запуск MySQL Community Server на виртуальной машине
Для установки, настройки и запуска MySQL Community Server выполните следующие действия:

1. После подключения к виртуальной машине с помощью удаленного рабочего стола, откройте **Internet Explorer** из меню **Пуск**. 

2. Нажмите кнопку **Сервис** в правом верхнем углу. В окне **Свойства браузера** выберите вкладку **Безопасность**, выберите значок **Надежные сайты**, а затем нажмите кнопку **Сайты**. Добавьте *http://\*.mysql.com* в список надежных сайтов.

3. Перейдите на страницу загрузки [Download MySQL Community Server][MySQLDownloads].

4. Выберите **Microsoft Windows** в **Platform** раскрывающемся меню и щелкните **Выбрать**.

5. Найти самый последний выпуск **Windows (x86, 64-разрядная), установщик MSI** и нажмите кнопку **Загрузка**. 

6. Выберите **Нет, просто начать загрузку!** (или зарегистрируйте учетную запись).  Если будет предложено, выберите сайт зеркальной загрузки установщика MySQL и сохраните установщик на рабочем столе.

7. Дважды щелкните файл установщика на рабочем столе, чтобы начать установку.

8. Нажмите кнопку **Далее**.

	![MySQL Setup][MySQLInstall1]

9. Примите условия лицензионного соглашения и нажмите кнопку **Далее**.

	![MySQL Setup][MySQLInstall2]

10. Нажмите кнопку **Обычная** для установки общих компонентов.

	![MySQL Setup][MySQLInstall3]

11. Нажмите **Установить**.

	![MySQL Setup][MySQLInstall4]

12. Запустите мастер настройки MySQL и щелкните **Далее**.

	![Configure MySQL][MySQLConfig1]

13. Выберите **Подробные настройки** и щелкните Далее.

	![Configure MySQL][MySQLConfig2]

14. Выберите **Сервер**, если вы планируете запустить MySQL вместе с другими приложениями на сервере, или выберите вариант, который наилучшим образом соответствует вашим потребностям.  Нажмите кнопку **Далее**.

	![Configure MySQL][MySQLConfig3]

15. Выберите **Многофункциональная база данных** или выберите вариант, который наилучшим образом соответствует вашим потребностям.  Нажмите **Далее**.

	![Configure MySQL][MySQLConfig4]

16. Выберите диск с данными, подсоединенный ранее.

	![Configure MySQL][MySQLConfig5]

17. Выберите **Поддержка принятия решений (DSS)/OLAP** или выберите вариант, который наилучшим образом соответствует вашим потребностям.  Нажмите кнопку **Далее**.

	![Configure MySQL][MySQLConfig6]

18. Выберите **Включение сети TCP/IP** и **Добавить исключения брандмауэра для порта** (это создаст правила входящего трафика в брандмауэре Windows с именем **сервер MySQL**).

	![Configure MySQL][MySQLConfig7]

19. Выберите **Максимальная поддержка различных языков**, если необходимо хранить текст на различных языках, или выберите вариант, который наилучшим образом соответствует вашим потребностям.  Нажмите кнопку **Далее**.

	![Configure MySQL][MySQLConfig8]

20. Выберите **Установить как службу Windows** и **Автоматически запустить сервер MySQL**.  Кроме того, выберите **Включить каталог Bin в Windows PATH**. Нажмите кнопку **Далее**.

	![Configure MySQL][MySQLConfig9]

21. Введите корневой пароль. Не отмечайте пункты **Разрешить доступ с правами корневого пользователя с удаленных компьютеров** или **Создать анонимную учетную запись**.  Нажмите кнопку **Далее**.

	![Configure MySQL][MySQLConfig10]

22. Щелкните **Выполнить** и дождитесь завершения настройки.

	![Configure MySQL][MySQLConfig11]

23. Нажмите кнопку **Готово**.

	![Configure MySQL][MySQLConfig12]

24. Щелкните **Пуск** и выберите **Клиент командной строки MySQL 5.x**, чтобы запустить клиент командной строки.

25.  При появлении запроса введите корневой пароль MySQL (который был установлен на предыдущем шаге), чтобы открыть командное окно, в котором можно вводить инструкции SQL для взаимодействия с базой данных.

26. Чтобы создать нового пользователя MySQL, выполните следующую команду в командной строке  **mysql>**:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Обратите внимание, что точка с запятой в конце строки является символом завершения команды.

27. Чтобы создать базу данных и предоставить разрешения пользователю `mysqluser`, выполните следующие команды:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Обратите внимание, что имена пользователей и пароли для базы данных используются только скриптами, подключающимися к базе данных.  Имена учетных записей базы данных могут не совпадать с учетными записями компьютера.

28. Чтобы войти с другого компьютера, выполите следующую команду:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	где `ip-address` - это IP-адрес компьютера, с которого осуществляется подключение к MySQL.
	
29. Чтобы выйти из клиента командной строки MySQL, выполните следующую команду:

		quit

30. После установки MySQL необходимо настроить конечную точку для удаленного доступа к MySQL. Выполните вход на портал [управления Microsoft Azure][AzurePreviewPortal]. На портале Azure щелкните **Виртуальные машины** и выберите имя новой виртуальной машины, а затем щелкните **Конечные точки** и   **Добавить конечную точку**.

	![Endpoints][AddEndPoint]

31. Выберите **Добавить конечную точку** и щелкните стрелку для продолжения.
	
	![Endpoints][AddEndPoint2]

32. Добавьте конечную точку с именем "MySQL", протоколом **TCP** и портами **Общий** и **Частный** со значением "3306". Щелкните значок галочки. Это позволит получать удаленный доступ к MySQL.
	
	![Endpoints][AddEndPoint3]

33. Можно удаленно подключиться к MySQL на виртуальной машине в Azure.  На локальном компьютере под управлением MySQL выполните следующую команду для входа в систему в качестве пользователя **mysqluser**, созданного на предыдущих этапах:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Например, при использовании виртуальной машины, созданной в этом руководстве, команда должна быть такой:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Сводка

В этом учебнике вы научились создавать виртуальную машину Windows 2008 R2 и удаленно подключаться к ней. Было также показано, как установить и настроить MySQL на виртуальной машине, создать базу данных и нового пользователя MySQL. Дополнительные сведения о MySQL см. в [документации по MySQL](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLInstall1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
[MySQLInstall2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
[MySQLInstall3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
[MySQLInstall4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
[MySQLConfig1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
[MySQLConfig2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
[MySQLConfig3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
[MySQLConfig4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
[MySQLConfig11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
[MySQLConfig12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
[AddEndPoint]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
[AddEndPoint2]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
[AddEndPoint3]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
