<properties
	pageTitle="Подготовка виртуальной машины SQL Server | Microsoft Azure"
	description="В этом руководстве объясняется, как создавать и настраивать виртуальные машины SQL Server в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="jroth"/>

# Подготовка виртуальной машины SQL Server в Azure

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

## Обзор

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.

Коллекция виртуальных машин в Azure включает в себя несколько образов, содержащих Microsoft SQL Server. Можно выбрать один из образов виртуальных машин из коллекции и несколькими щелчками подготовить виртуальную машину в среде Azure.

Изучив данный учебник, вы научитесь:

* [Подключение к классическому порталу Azure и подготовка виртуальной машины из коллекции](#Provision)
* [Открытие виртуальной машины с помощью удаленного рабочего стола, а также выполнение полной установки](#RemoteDesktop)
* [Выполнение шагов настройки для подключения к виртуальной машине с помощью среды SQL Server Management Studio на другом компьютере](#SSMS)
* [Дальнейшие действия](#Optional)

>[AZURE.NOTE]В этой статье описывается, как подготовить виртуальную машину SQL Server с помощью имеющегося портала. Тем не менее, вы также можете создавать виртуальные машины SQL Server и управлять ими на [новом портале](https://manage.windowsazure.com). Новые портал имеет некоторые преимущества, например использование хранилища класса Premium по умолчанию, и другие параметры, например автоматическое исправление, автоматическое резервное копирование и конфигурации AlwaysOn. В последующих статьях представлены пошаговые инструкции.

##<a id="Provision">Подготовка виртуальной машины SQL Server из коллекции</a>

1. Перейдите на [классический портал Azure](http://manage.windowsazure.com), используя свою учетную запись. Если у вас нет учетной записи Azure, используйте [бесплатную пробную версию Azure](http://azure.microsoft.com/pricing/free-trial/).

2. На классическом портале Azure в нижней левой части веб-страницы щелкните **+СОЗДАТЬ**, **ВЫЧИСЛЕНИЯ**, затем **ВИРТУАЛЬНАЯ МАШИНА** и **ИЗ КОЛЛЕКЦИИ**.

3. На странице **Выберите образ** щелкните **SQL SERVER**. Выберите образ SQL Server. Щелкните стрелку перехода вперед в правой нижней части страницы.

	![Выбор образа](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Самую последнюю информацию о поддерживаемых образах SQL Server в Azure см. в статье [Общие сведения об SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

>[AZURE.NOTE]Виртуальную машину, созданную с помощью ознакомительной версии образа платформы SQL Server, нельзя обновить в коллекции до образа с поминутной оплатой. Можно выбрать один из двух вариантов:
>
> - Вы можете создать новую виртуальную машину с помощью выпуска SQL Server с поминутной оплатой из коллекции и перенести файлы базы данных на эту новую виртуальную машину, выполнив шаги, описанные в статье [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database).
> - Либо вы можете обновить существующий экземпляр ознакомительного выпуска SQL Server до другого выпуска SQL Server в рамках соглашения [Перемещение лицензий в рамках программы Software Assurance в Azure](http://azure.microsoft.com/pricing/license-mobility/), следуя процедуре, описанной в статье [Обновление SQL Server до другого выпуска](https://msdn.microsoft.com/library/cc707783.aspx). Сведения о покупке лицензионной копии SQL Server см. в разделе [Как приобрести SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).

4. На первой странице **Конфигурация виртуальной машины** укажите следующие сведения:
	- **ДАТА ВЫПУСКА ВЕРСИИ**. Если доступно несколько образов, выберите самый последний из них.
	- Уникальное **ИМЯ ВИРТУАЛЬНОЙ МАШИНЫ**.
	- В окне **НОВОЕ ИМЯ ПОЛЬЗОВАТЕЛЯ** введите уникальное имя пользователя для учетной записи локального администратора компьютера.
	- В поле **НОВЫЙ ПАРОЛЬ** введите надежный пароль.
	- В поле **ПОДТВЕРЖДЕНИЕ ПАРОЛЯ** введите пароль еще раз.
	- Выберите соответствующий **РАЗМЕР** из раскрывающегося списка.

	![Конфигурация виртуальной машины](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]Размер виртуальной машины указывается во время подготовки.
 	>
	> - Для производственных рабочих нагрузок рекомендуем использовать хранилище класса Premium следующих минимальных рекомендуемых размеров: **DS3** для редакции SQL Server Enterprise и **DS2** — для SQL Server Standard. Дополнительные сведения см. в статье [Рекомендации по оптимизации производительности SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-performance-best-practices.md).
	> - Выбранный размер виртуальной машины ограничивает количество дисков данных, которые можно настроить. Самую последнюю информацию о доступных размерах виртуальных машин и о количестве дисков данных, которые можно присоединить к виртуальной машине, см. в разделе [Размеры виртуальных машин для Azure](virtual-machines-size-specs.md).

5. После ввода сведений о конфигурации вашей виртуальной машины нажмите стрелку "Далее" в правом нижнем углу страницы, чтобы продолжить.

5. На второй странице **Конфигурации виртуальной машины** настройте ресурсы сети, хранилища и доступность:
	- В окне **Облачная служба** выберите **Создать новую облачную службу**.
	- В поле **DNS-имя облачной службы** укажите первую часть выбранного DNS-имени таким образом, чтобы общее имя соответствовало формату **TESTNAME.cloudapp.net**
	- Выберите значение в поле **ПОДПИСКА**, если у вас имеется выбор из нескольких подписок. Этот выбор определяет доступные **учетные записи хранения**.
	- В поле **РЕГИОН, ТЕРРИТОРИАЛЬНАЯ ГРУППА, ВИРТУАЛЬНАЯ СЕТЬ** выберите регион, где будет размещаться этот виртуальный образ.
	- В окне **Учетная запись хранения** создайте учетную запись автоматически или выберите ее из списка. Измените значение **ПОДПИСКА** для просмотра дополнительных учетных записей.
	- В поле **ГРУППА ДОСТУПНОСТИ** выберите **(нет)**.
	- Прочтите и примите условия.


6. Щелкните стрелку "Далее" для продолжения.


7. Чтобы продолжить, щелкните флажок в правом нижнем углу.

8. Подождите, пока Azure подготавливает виртуальную машину. Состояние виртуальной машины должно изменяться следующим образом:

	- **Запуск (подготовка)**
	- **Stopped**
	- **Запуск (подготовка)**
	- **Выполнение (подготовка)**
	- **Выполнение**


##<a id="RemoteDesktop">Открытие виртуальной машины с помощью удаленного рабочего стола для завершения настройки</a>

1. После завершения подготовки щелкните имя виртуальной машины, чтобы перейти к странице ПАНЕЛЬ МОНИТОРИНГА. В нижней части страницы щелкните **Подключиться**.

2. Нажмите кнопку **Открыть**.

	![Нажмите кнопку «Открыть».](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. В диалоговом окне **Безопасность Windows** нажмите **Другая учетная запись**.

	![Нажмите «Другая учетная запись»](./media/virtual-machines-provision-sql-server/credentials.png)

4. В качестве доменного имени используйте имя компьютера, после чего укажите имя администратора в таком формате: `machinename\username`. Введите пароль и подключитесь к компьютеру.

4. При первом входе в систему будет выполнено несколько процессов, включая настройку рабочего стола, обновления Windows и выполнение задач начальной настройки Windows (sysprep). После выполнения задач sysprep для Windows будут выполнены задачи по настройке SQL Server. Выполнение этих задач может вызвать задержку на несколько минут. `SELECT @@SERVERNAME` может не возвратить правильное имя, пока не завершится установка SQL Server, а SQL Server Management Studio может не отображаться на начальной странице.

После подключения к виртуальной машине с помощью удаленного рабочего стола Windows виртуальная машина будет работать аналогично любому другому компьютеру. Обычным способом подключитесь к экземпляру SQL Server по умолчанию с помощью программы SQL Server Management Studio (запущенной на виртуальной машине).

##<a id="SSMS">Подключение к экземпляру виртуальной машины SQL Server в среде SSMS на другом компьютере</a>

Ниже показано, как подключиться к экземпляру SQL Server через Интернет с помощью SQL Server Management Studio (SSMS). Однако, чтобы сделать виртуальную машину SQL Server доступной для приложений, выполняющихся как локально, так и в классической модели развертывания Azure, необходимо выполнить те же действия. При развертывании виртуальной машины в модели диспетчера ресурсов см. раздел [Подключение к виртуальной машине SQL Server в Azure (диспетчер ресурсов)](virtual-machines-sql-server-connectivity-resource-manager.md).

Чтобы подключиться к экземпляру SQL Server из другой виртуальной машины или через Интернет, необходимо выполнить задачи, описанные в следующих разделах:

- [Создание конечной точки TCP для виртуальной машины](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Открытие портов TCP в брандмауэре Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Настройка SQL Server для прослушивания через протокол TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Настройка SQL Server для аутентификации в смешанном режиме](#configure-sql-server-for-mixed-mode-authentication)
- [Создание имен входа для аутентификации SQL Server](#create-sql-server-authentication-logins)
- [Определение DNS-имени виртуальной машины](#determine-the-dns-name-of-the-virtual-machine)
- [Подключение к ядру СУБД с другого компьютера](#connect-to-the-database-engine-from-another-computer)

Процедура подключения показана на следующей схеме:

![Подключение к виртуальной машине SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине (классическое развертывание с конечной точкой TCP)](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Подключение к SQL Server на виртуальной машине (классическое развертывание)](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a id="cdea">Подключение к ядру СУБД из приложения</a>

Если вы можете подключиться к экземпляру SQL Server, работающему на виртуальной машине Azure, с использованием Management Studio, вы также должны иметь возможность подключиться с помощью строки подключения следующего вида.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Более подробные сведения см. в разделе [Устранение неполадок при подключении к ядру СУБД SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

##<a id="Optional">Дальнейшие действия</a>

Теперь вы знаете, как создавать и настраивать SQL Server на виртуальной машине Azure с помощью образа платформы. Во многих случаях следующим этапом является миграция баз данных на новую виртуальную машину SQL. Руководство по миграции баз данных см. в статье [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-migrate-onpremises-database.md).

Ниже перечислены дополнительные источники информации об SQL Server на виртуальных машинах Azure.

### Рекомендуемые ресурсы по работе SQL Server на виртуальных машинах Azure:
- [Обзор. SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md)

- [Подключение к виртуальной машине SQL Server в Azure](virtual-machines-sql-server-connectivity.md)

- [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-sql-server-performance-best-practices.md)

- [Вопросы безопасности SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-security-considerations.md)

### Высокая доступность и аварийное восстановление:
- [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Резервное копирование и восстановление SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-backup-and-restore.md)

### Рабочие нагрузки SQL Server в Azure:
- [SQL Server Business Intelligence на виртуальных машинах Azure](virtual-machines-sql-server-business-intelligence.md)

### Технические документы:
- [Общее представление о Базе данных SQL Azure и SQL Server на виртуальных машинах Azure](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=AcomDC_0107_2016-->