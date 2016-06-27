<properties
	pageTitle="Создание виртуальной машины базы данных Oracle при помощи классического портала Azure | Microsoft Azure"
	description="Узнайте, как создать виртуальную машину базы данных Oracle при помощи классической модели развертывания и портала Azure."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="Windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rclaus" />

#Создание виртуальной машины базы данных Oracle в Azure

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

В следующем примере показано, как создать базу данных Oracle Database на виртуальной машине Windows Server, которая была предварительно создана и на которую была установлена база данных Oracle версии 12c. Этот процесс состоит из двух этапов. Сначала необходимо подключить виртуальную машину, а затем создать в ней базу данных Oracle Database. В приведенном примере используется база данных Oracle версии 12c, но шаги для версии 11g практически идентичны.


##Для создания базы данных с использованием виртуальной машины базы данных Oracle в Azure

1.	Войдите на [портал Azure](https://portal.azure.com/).

2.	Щелкните **Виртуальные машины**.

3.	Щелкните по имени виртуальной машины, в которую нужно войти.

4.	Щелкните **Подключить**.

5.	Отвечайте на запросы, появляющиеся при подключении к виртуальной машине. При получении запроса на ввод имени пользователя и пароля администратора используйте значения, которые были указаны при создании виртуальной машины.

6.	Создайте переменную среды **ORACLE\_HOSTNAME** со значением, соответствующим имени компьютера виртуальной машины. Создать переменную среды можно, выполнив следующие действия:

	а. Нажмите кнопку **Пуск** Windows, наберите **панель управления**, щелкните значок **Панель управления**, затем последовательно щелкните **Система и безопасность**, **Система** и **Дополнительные параметры системы**.

	b. Щелкните вкладку **Дополнительно**, а затем нажмите кнопку **Переменные среды**.

	c. В разделе **Системные переменные** нажмите кнопку **Создать**, чтобы создать переменную.

	г) В диалоговом окне **Новая системная переменная** введите **ORACLE\_HOSTNAME** в качестве имени переменной и затем в качестве значения введите имя компьютера виртуальной машины. Чтобы определить имя компьютера, откройте командную строку и выполните **SET COMPUTERNAME** (выходные данные этой команды будут содержать имя компьютера).

	д. Нажмите кнопку **ОК**, чтобы сохранить новую переменную среды и закрыть диалоговое окно **Новая системная переменная**.

	Е. Закройте другие диалоговые окна, открытые из панели управления.

7.	Нажмите кнопку **Пуск** Windows и введите **Database Configuration Assistant**. Щелкните значок **Database Configuration Assistant**.

8.	В мастере **Database Configuration Assistant** введите значения, необходимые для каждого шага в диалоговом окне.

	а. **Шаг 1.** Щелкните **Создать базу данных** и нажмите кнопку **Далее**.

		![](media/virtual-machines-windows-classic-create-oracle-database/image5.png)

	b. **Шаг 2.** Введите значение в поле **Имя глобальной базы данных**. Введите и подтвердите значение в поле **Пароль администратора**. Этот пароль будет использован для пользователя **SYSTEM** базы данных Oracle. Снимите флажок **Создать как базу данных контейнера**. Нажмите кнопку **Далее**.

		![](media/virtual-machines-windows-classic-create-oracle-database/image6.png)

	c. **Шаг 3.** Проверка необходимых компонентов должна быть выполнена автоматически, затем вы перейдете к **шагу 4**.

	г) **Шаг 4.** Проверьте параметры в области **Создание базы данных — сводка** и нажмите кнопку **Готово**.

		![](media/virtual-machines-windows-classic-create-oracle-database/image7.png)
	д. **Шаг 5.** На **Странице хода выполнения** будет отображаться ход создания вашей базы данных.

		![](media/virtual-machines-windows-classic-create-oracle-database/image8.png)
	Е. После создания базы данных вы сможете воспользоваться окном **Управление паролями**. При необходимости измените пароли, а затем закройте диалоговые окна, чтобы выйти из мастера **Database Configuration Assistant**.

##Подтверждение установки базы данных

1.	Находясь в виртуальной машине, запустите командную строку SQL Plus. Нажмите кнопку *Пуск** Windows, затем введите **SQL Plus**. Щелкните значок **SQL Plus**.

2.	При появлении запроса войдите в систему с именем пользователя **SYSTEM** и паролем, указанным при создании базы данных Oracle.

3.	Выполните следующую команду в командной строке SQL Plus.

		**select * from GLOBAL\_NAME;**

	Результатом должно быть глобальное имя созданной базы данных.

	![](media/virtual-machines-windows-classic-create-oracle-database/image9.png)

##Разрешение удаленного доступа к базе данных
Чтобы разрешить удаленный доступ к базе данных (например, с клиентского компьютера, на котором выполняется код Java), будет необходимо запустить прослушиватель базы данных, открыть порт 1521 в брандмауэре виртуальной машины и создать общедоступную конечную точку для порта 1521.

### Запуск прослушивателя базы данных
1.	Войдите в систему на виртуальной машине.

2.	Откройте окно командной строки.

3.	В командной строке выполните следующую команду:

		**lsnrctl start**

> [AZURE.NOTE] Для проверки состояния прослушивателя можно выполнить команду **lsnrctl status**. Для остановки прослушивателя выполните команду **lsnrctl stop**.

### Открытие порта 1521 в брандмауэре виртуальной машины

1.	Не выходя из виртуальной машины, нажмите кнопку **Пуск** Windows, введите **Брандмауэр Windows в режиме повышенной безопасности**, затем щелкните значок **Брандмауэр Windows в режиме повышенной безопасности**. Откроется консоль управления **Брандмауэра Windows в режиме повышенной безопасности**.

2.	В левой области консоли управления брандмауэра щелкните **Правила для входящих подключений** (если вы не видите пункт **Правила для входящих подключений**, разверните верхний узел в левой области) и щелкните **Создать правило** в правой области.

3.	В поле **Тип правила** выберите **Порт**, а затем нажмите кнопку **Далее**.

4.	В поле **Протокол и порты** выберите **Протокол TCP**, выберите **Определенные локальные порты**, введите номер порта **1521** и нажмите кнопку **Далее**.

5.	Выберите **Разрешить подключение** и нажмите **Далее**.

6.	Примите значения по умолчанию для профилей, к которым применяется правило, и нажмите кнопку **Далее**.

7.	Укажите имя правила и необязательное описание, затем нажмите кнопку **Готово**.

### Создание общедоступной конечной точки для порта 1521

1.	Войдите на [портал Azure](https://portal.azure.com/).

2.	Щелкните **Обзор**.

3.  Щелкните **Виртуальные машины**.

4.	Выберите виртуальную машину.

5.	Щелкните **Параметры**.

6.	Нажмите кнопку **Конечные точки**.

7.	Щелкните **Добавить**.

8.	Укажите имя конечной точки:

	а. Укажите протокол **TCP**.

	b. Укажите номер **1521** для общего порта.

	c. Укажите номер **1521** для частного порта.

9.	Значения остальных параметров оставьте без изменений.

10. Нажмите кнопку **ОК**.

##Включение удаленного доступа Oracle Database Enterprise Manager
Если вы хотите разрешить удаленный доступ к Oracle Database Enterprise Manager, откройте в брандмауэре порт 5500 и создайте конечную точку виртуальной машины для порта 5500 на классическом портале Azure (следуя описанным выше указаниям по открытию порта 1521 и созданию конечной точки для порта 1521). Затем, чтобы запустить Oracle Enterprise Manager с удаленного компьютера, откройте в браузере URL-адрес вида `http://<<unique_domain_name>>:5500/em`.

> [AZURE.NOTE] (Значение *unique\_domain\_name* можно определить на [классическом портале Azure](https://portal.azure.com/), щелкнув **Виртуальные машины** и выбрав виртуальную машину, которая используется для запуска базы данных Oracle.)

##Настройка пакетов Popular Options и Advanced Options
Если вы выбрали пакет **Oracle Database with Popular Options** или **Oracle Database with Advanced Options**, следующим шагом будет настройка дополнительных функций в установленном экземпляре Oracle. Указания по их настройке в Windows см. в документации по Oracle, так как конфигурации могут сильно отличаться в зависимости от потребностей каждого отдельного компонента.

**Пакет Oracle Database with Popular Options** включает в себя Oracle Database Enterprise Edition и лицензированные экземпляры [Partitioning](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), [Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), [Oracle Tuning Pack for Database](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), [Oracle Diagnostics Pack for Database](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ) и [Oracle Lifecycle Management Pack for Database](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html).

**Пакет Oracle Database with Advanced Options** включает в себя лицензированные экземпляры всех компонентов пакета Popular Options, а также [Advanced Compression](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), [Advanced Security](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), [Label Security](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), [Database Vault](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), [Advanced Analytics](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), [OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [Spatial and Graph](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), [In-Memory Database Cache](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), [Data Masking Pack](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB) и пакет Oracle Test Data Management Pack (в составе пакета Data Masking Pack).

##Дополнительные ресурсы
Теперь, настроив виртуальную машину и создав базу данных, вы можете ознакомиться со следующими разделами, чтобы получить дополнительную информацию.

-	[Образы виртуальных машин Oracle. Различные рекомендации](virtual-machines-windows-classic-oracle-considerations.md)

-	[Библиотека документации по Oracle Database 12ce](http://www.oracle.com/pls/db1211/homepage)

-	[Подключение к базе данных Oracle из приложения Java](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-	[Образы виртуальных машин Oracle для Azure](virtual-machines-linux-classic-oracle-images.md)

-	[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0615_2016-->