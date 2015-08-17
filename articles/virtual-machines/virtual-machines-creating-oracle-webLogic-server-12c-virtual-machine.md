<properties title="Creating an Oracle WebLogic Server 12c Virtual Machine in Azure" pageTitle="Создание виртуальной машины Oracle WebLogic Server 12c в Azure" description="Пошагово создайте пример образа Oracle WebLogic Server 12c под управлением Windows Server 2012 в Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Создание виртуальной машины Oracle WebLogic Server 12c в Azure
В следующем примере показано, как можно создать в Azure виртуальную машину на основе предоставленного корпорацией Майкрософт образа Oracle WebLogic Server 12c под управлением Windows Server 2012.

##Создание виртуальной машины Oracle WebLogic Server 12c в Azure

1. Войдите на [портал Azure](https://ms.portal.azure.com/).

2. Щелкните **Marketplace**, щелкните **Среда выполнения приложений**, затем введите **Oracle** в поле поиска.

3.	Выберите образ **Oracle WebLogic Server 12c Standard Edition на Windows Server 2012** или **Oracle WebLogic Server 12c Enterprise Edition на Windows Server 2012**. Просмотрите информацию о выбранном образе (например, минимальный рекомендуемый размер) и нажмите кнопку **Далее**.

4.	Укажите **Имя узла** для виртуальной машины.

5.	Укажите **Имя пользователя** для виртуальной машины. Обратите внимание, что это пользователь для удаленного входа в виртуальную машину. Это не имя пользователя базы данных Oracle.

6.	Укажите и подтвердите пароль для виртуальной машины или предоставьте открытый ключ SSH.

7.	Выберите **ценовую категорию**. Обратите внимание, что по умолчанию отображаются рекомендуемые ценовые категории. Чтобы увидеть все варианты конфигурации, нажмите кнопку **Просмотреть все** в правом верхнем углу.

8.	При необходимости задайте [дополнительную конфигурацию](https://msdn.microsoft.com/library/azure/dn763935.aspx), приняв к сведению следующие рекомендации:
	1. Оставьте значение **Учетная запись хранения** как есть, чтобы создать новую учетную запись хранения с именем виртуальной машины.
	2. Оставьте в поле **Группа доступности** значение «Не настроено».
	3. Пока не добавляйте **конечные точки**.

9.	Выберите или создайте [группу ресурсов](resource-group-portal.md).

10. Выберите **подписку**.

11. Выберите **расположение**.


##Настройка виртуальной машины Oracle WebLogic Server 12c в Azure

1. Войдите на [портал Azure](https://ms.portal.azure.com/).

2.	Щелкните **Виртуальные машины**.

3.	Щелкните имя виртуальной машины, в которую нужно войти.

4.	Щелкните **Подключить**.

5.	Отвечайте на запросы, появляющиеся при подключении к виртуальной машине. При получении запроса на ввод имени и пароля администратора используйте значения, которые были указаны при создании виртуальной машины.

6.	В диалоговом окне **WebLogic Platform QuickStart** (Краткое руководство по платформе WebLogic) щелкните **Getting Started with WebLogic Server** (Приступая к работе с сервером WebLogic). (Если диалоговое окно **WebLogic Platform QuickStart** (Краткое руководство по платформе WebLogic) еще не открыто, откройте его. Для этого нажмите кнопку **Пуск** Windows, введите **Start Admin Server for WebLogic Server Domain** и щелкните значок **Start Admin Server for WebLogic Server Domain** (Запуск сервера администрирования для домена WebLogic Server).)

7.	В **диалоговом окне приветствия** выберите **Create a new WebLogic domain** (Создать новый домен WebLogic) и нажмите кнопку **Next** (Далее).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image10.png)

8.	В диалоговом окне **Select Domain Source** (Выбор источника домена) примите значения по умолчанию и нажмите кнопку **Next** (Далее).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image11.png)

9.	В диалоговом окне **Specify Domain Name and Location** (Указание имени и расположения домена) примите значения по умолчанию и нажмите кнопку **Next** (Далее).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image12.png)

10.	В диалоговом окне **Configure Administrator User Name and Password** (Настройка имени пользователя и пароля администратора):

	1.	[Необязательно.] Измените имя пользователя **weblogic** по своему выбору.

	2.	Укажите и подтвердите пароль для администратора WebLogic Server.

	3.	Нажмите кнопку **Далее**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image13.png)

11.	В диалоговом окне **Configure Server Start Mode and JDK** (Настройка режима запуска сервера и JDK) выберите **Production Mode** (Рабочий режим), выберите доступный JDK (или перейдите к JDK, при необходимости) и нажмите кнопку **Next** (Далее).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image14.png)

12.	В диалоговом окне **Select Optional Configuration** (Выбор дополнительной конфигурации), не выбирая какие-либо параметры, нажмите кнопку **Next** (Далее).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image15.png)

13.	В диалоговом окне **Configuration Summary** (Сводка по конфигурации) нажмите кнопку **Create** (Создать).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image16.png)

14.	В диалоговом окне **Creating Domain** (Создание домена) установите флажок **Start Admin Server** (Запустить сервер администрирования) и нажмите кнопку **Done** (Готово).

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image17.png)

15.	Запустится командная строка **startWebLogic.cmd**. При появлении запроса укажите имя пользователя и пароль WebLogic.

##Установка приложения на виртуальную машину Oracle WebLogic Server 12c в Azure
1.	Не выходя из виртуальной машины, скопируйте в пример shoppingcart.war, доступный по адресу http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war, на локальный компьютер. Например, создайте папку с именем **c:\\mywar** и сохраните WAR-файл, полученный по адресу http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war, в **c:\\mywar**.

2.	Откройте **консоль администрирования WebLogic Server**, http://localhost:7001/console. При появлении запроса укажите имя пользователя и пароль WebLogic.

3.	В **консоли администрирования WebLogic Server** последовательно щелкните **Lock & Edit** (Заблокировать и изменить), **Deployments** (Развертывания) и нажмите кнопку **Install** (Установить).

4.	В поле **Path** (Путь) введите **c:\\myway\\shoppingcart.war**.

	![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image18.png)

	Нажмите кнопку **Далее**.

5.	Выберите **Install this deployment as an application** (Установить это развертывание как приложение) и нажмите кнопку **Next** (Далее).

6.	Нажмите кнопку **Готово**

7.	В **консоли администрирования WebLogic Server** нажмите кнопку **Save** (Сохранить) и щелкните **Activate Changes** (Активировать изменения).

8.	Щелкните **Deployments** (Развертывания), выберите **shoppingcart**, щелкните **Start** (Запуск), затем щелкните **Service All Requests** (Обслуживать все запросы). При появлении запроса на подтверждение щелкните **Yes** (Да).

9.	Чтобы просмотреть запущенное локально приложение shoppingcart, откройте в браузере адрес <http://localhost:7001/shoppingcart>.

10.	Создание конечной точки для виртуальной машины:

	1. Войдите на [портал Azure](https://ms.portal.azure.com/).

	2.	Щелкните **Обзор**.

	3.	Щелкните **Виртуальные машины**.

	4.	Выберите виртуальную машину.

	5.	Щелкните **Параметры**.

	6.	Нажмите кнопку **Конечные точки**.

	7.	Щелкните **Добавить**.

	8.	Укажите имя конечной точки:

		1. Укажите протокол **TCP**.

		2. Укажите номер **80** для общего порта.

		3. Укажите номер **7001** для частного порта.

	9.	Значения остальных параметров оставьте без изменений.

	10. Нажмите кнопку **ОК**.

11.	Разрешите в брандмауэре входящие подключения через порт 7001:

	1.	Войдите в виртуальную машину.

	2.	Нажмите кнопку **Пуск** Windows, введите **брандмауэр Windows в режиме повышенной безопасности**, затем щелкните значок **Брандмауэр Windows в режиме повышенной безопасности**. Откроется консоль управления **Брандмауэр Windows в режиме повышенной безопасности**.

	3.	В консоли управления брандмауэра в левой области щелкните **Правила для входящих подключений** (если вы не видите пункт **Правила для входящих подключений**, разверните верхний узел в левой области) и щелкните «Создать правило» в области справа.

	4.	В разделе **Тип правила** выберите **Для порта** и нажмите кнопку **Далее**.

	5.	В разделе **Протокол и порты** выберите **Протокол TCP**, выберите **Определенные локальные порты**, введите номер порта **7001** и нажмите кнопку **Далее**.

	6.	Выберите **Разрешить подключение** и нажмите **Далее**.

	7.	Примите значения по умолчанию для профилей, к который применяется правило, и нажмите кнопку **Далее**.

	8.	Укажите имя правила и необязательное описание, затем нажмите кнопку **Готово**.

12.	Чтобы увидеть выполнение приложения shoppingcart в Интернете, откройте в браузере URL-адрес вида `http://<<unique_domain_name>>/shoppingcart`. (Значение <<*уникальное\_доменное\_имя*>> можно определить на [портале Azure](https://ms.portal.azure.com/), щелкнув **Виртуальные машины** и выбрав виртуальную машину, которая используется для запуска Oracle WebLogic Server.)


##Дополнительные ресурсы
Теперь, настроив виртуальную машину, на которой выполняется Oracle WebLogic Server, вы можете ознакомиться следующими разделами, чтобы получить дополнительную информацию.

-	[Образы виртуальных машин Oracle. Различные рекомендации](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-	[Документация по продукту Oracle WebLogic Server](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Oracle WebLogic Server 12c с использованием Linux в Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[Образы виртуальных машин Oracle для Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=August15_HO6-->