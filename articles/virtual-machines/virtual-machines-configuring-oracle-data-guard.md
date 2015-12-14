<properties
	pageTitle="Настройка Oracle Data Guard на виртуальных машинах | Microsoft Azure"
	description="Изучите учебник по установке и реализации Oracle Data Guard на виртуальных машинах Azure для обеспечения высокого уровня доступности и аварийного восстановления."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Настройка Oracle Data Guard для Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


В этом учебнике показано, как настроить и реализовать Oracle Data Guard в среде виртуальных машин Azure, чтобы обеспечить высокий уровень доступности и аварийное восстановление. Учебник посвящен односторонней репликации для баз данных Oracle, не являющихся кластерами реальных приложений (RAC).

Oracle Data Guard поддерживает защиту данных и аварийное восстановление для базы данных Oracle. Это простое и высокопроизводительное решение для аварийного восстановления, защиты данных и обеспечения высокой доступности всей базы данных Oracle.

В этом учебнике предполагается, что вы уже ознакомились с концепциями высокого уровня доступности и аварийного восстановления баз данных Oracle как в теории, так и на практике. Информацию см. на [веб-сайте Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) и в руководстве [Основные понятия и администрирование Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17022/create_ps.htm).

Кроме того, для работы с учебником вы должны выполнить следующие предварительные требования:

- Ознакомиться с подразделом "Рекомендации по высокой доступности и аварийному восстановлению" раздела [Различные рекомендации по образам виртуальных машин Oracle](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md). Обратите внимание, что в настоящее время Azure поддерживает автономные экземпляры базы данных Oracle, но не кластеры реальных приложений Oracle (Oracle RAC).

- Создать две виртуальные машины в Azure с помощью той же платформы, которая предоставляется образом Oracle Enterprise Edition в Windows Server. Информацию см. в статьях [Создание виртуальной машины Oracle Database 12c в Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md) и [Виртуальные машины Azure](http://azure.microsoft.com/documentation/services/virtual-machines/). Убедиться, что эти виртуальные машины находятся в [одной облачной службе](virtual-machines-load-balance.md) и [одной виртуальной сети](azure.microsoft.com/documentation/services/virtual-network/), чтобы они могли обращаться друг к другу посредством постоянного частного IP-адреса. Кроме того, рекомендуем размещать виртуальные машины в одной [группе доступности](virtual-machines-manage-availability.md), так как это позволяет Azure помещать их в отдельные домены сбоя и домены обновления. Обратите внимание, что Oracle Data Guard доступен только в выпуске Oracle Database Enterprise Edition. Каждая виртуальная машина должна иметь не менее 2 ГБ памяти и 5 ГБ места на диске. Последние сведения о предоставляемых платформой размерах виртуальных машин см. в статье [Размеры виртуальных машин в Azure](http://msdn.microsoft.com/library/dn197896.aspx). Если для ваших виртуальных машин требуются дополнительные тома диска, можно подключить дополнительные диски. Информацию см. в разделе [Подключение диска данных к виртуальной машине](storage-windows-attach-disk.md).

- На классическом портале Azure имя Machine1 назначено для основной виртуальной машины, а Machine2 — для резервной.

- Установить переменную среды **ORACLE\_HOME**, чтобы она указывала на один корневой путь установки Oracle на основной и резервной виртуальных машинах, например `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Войти на сервер Windows как участник группы **Administrators** или **ORA\_DBA**.

Изучив данный учебник, вы научитесь:

Реализация физической среды с резервной базой данных

1. Создание базы данных-источника.

2. Подготовка базы данных-источника для создания резервной базы данных.

	1. Включение принудительного ведения журнала.

	2. Создание файла пароля.

	3. Настройка резервного журнала повторяемых операций.

	4. Включить архивирование

	5. Установка параметров инициализации базы данных-источника.

Создание физической резервной базы данных

1. Подготовка файла параметров инициализации для резервной базы данных.

2. Настройка прослушивателя и файла tnsnames для поддержки базы данных на основной и резервной виртуальных машинах.

	1. Настройка listener.ora на обоих серверах для хранения записей для обеих баз данных.

	2. Настройка tnsnames.ora на основной и резервной виртуальных машинах для хранения записей для базы данных-источника и резервной базы данных.

	3. Запуск прослушивателя и проверка tnsping на обеих виртуальных машин для обеих служб.

3. Запуск резервного экземпляра в состоянии nomount.

4. Использование RMAN для клонирования базы данных и создания резервной базы данных.

5. Запуск физической резервной базы данных в режиме управляемого восстановления.

6. Проверка физической резервной базы данных.

> [AZURE.IMPORTANT]Данный учебник был разработан и протестирован для следующей конфигурации оборудования и программного обеспечения.
>
>| | **База данных-источник** | **Резервная база данных** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Версия Oracle** | Oracle11g Enterprise Release (11.2.0.4.0) | Oracle11g Enterprise Release (11.2.0.4.0) |
>| **Имя виртуальной машины** | Machine1 | Machine2 |
>| **Операционная система** | Windows 2008 R2 | Windows 2008 R2 |
>| **ИД безопасности Oracle** | TEST | TEST\_STBY |
>| **Память** | Минимум 2 ГБ | Минимум 2 ГБ |
>| **Место на диске** | Минимум 5 ГБ | Минимум 5 ГБ |

Для последующих выпусков базы данных Oracle и Oracle Data Guard может потребоваться реализовать некоторые дополнительные изменения. Последние сведения о версиях см. в документации по [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) и [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) на веб-сайте Oracle.

##Реализация физической среды с резервной базой данных
### 1\. Создание базы данных-источника

- Создайте базу данных-источник TEST на основной виртуальной машине. Дополнительную информацию см. в статье «Создание и настройка базы данных Oracle».
- Подключитесь к базе данных как пользователь SYS с ролью SYSDBA в командной строке SQL*Plus и выполните следующую инструкцию, чтобы увидеть имя базы данных:

		SQL> select name from v$database;

		The result will display like the following:

		NAME
		---------
		TEST
- Затем запросите имена файлов базы данных из системного представления dba\_data\_files:

		SQL> select file_name from dba_data_files;
		FILE_NAME
		-------------------------------------------------------------------------------
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2\. Подготовка базы данных-источника для создания резервной базы данных

Перед созданием резервной базы данных рекомендуется убедиться, что база данных-источник настроена правильно. Ниже приведен список действий, которые необходимо выполнить.

1. Включение принудительного ведения журнала.
2. Создание файла пароля.
3. Настройка резервного журнала повторяемых операций.
4. Включить архивирование
5. Установка параметров инициализации базы данных-источника.

#### Включение принудительного ведения журнала.

Чтобы реализовать резервную базу данных, нам нужно включить параметр принудительного ведения журнала в базе данных-источнике. Этот параметр гарантирует, что даже в случае, если выполнена операция nologging, принудительное ведение журнала имеет приоритет, и все операции записываются в журналы повторяемых операций. Таким образом мы можем быть уверены, что все, происходящее в базе данных-источнике, регистрируется, и репликация в резервную базу данных включает все операции в базе данных-источнике. Выполните инструкцию alter database, чтобы включить принудительное ведение журнала:

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### Создание файла пароля

Чтобы можно было отправлять архивные журналы с основного сервера на резервный сервер и применять их, пароль пользователя sys на обоих серверах должен быть одинаковым. Поэтому создайте файл пароля в базе данных-источнике и скопируйте его на резервный сервер.

>[AZURE.IMPORTANT]В базе данных Oracle Database 12c имеется новый пользователь **SYSDG**, которого можно использовать для администрирования Oracle Data Guard. Дополнительные сведения см. в статье [Изменения в выпуске Oracle Database 12c](http://docs.oracle.com/cd/E16655_01/server.121/e10638/release_changes.htm).

Кроме того, проверьте, определена ли на виртуальной машине Machine1 среда ORACLE\_HOME. Если нет, задайте ее как переменную среды с помощью диалогового окна «Переменные среды». Чтобы открыть это диалоговое окно, запустите служебную программу **Система**, дважды щелкнув значок "Система" на **панели управления**. Затем перейдите на вкладку **Дополнительно** и нажмите кнопку **Переменные среды**. Нажмите кнопку **Создать** в разделе **Системные переменные**, чтобы установить переменные среды. После установки переменных среды закройте текущую командную строку Windows и откройте новую.

Выполните следующую инструкцию, чтобы перейти в каталог Oracle\_Home, например в C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database.

	cd %ORACLE_HOME%\database

Затем создайте файл пароля с помощью служебной программы создания файла пароля [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). В этой же командной строке Windows на виртуальной машине Machine1 выполните следующую команду, задав значение пароля как пароль пользователя **SYS**:

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Эта команда создает файл пароля с именем как PWDTEST.ora в каталоге ORACLE\_HOME\\database. Вы должны вручную скопировать этот файл в каталог %ORACLE\_HOME%\\database на виртуальной машине Machine2.

#### Настройка резервного журнала повторяемых операций

Теперь вам надо настроить резервный журнал повторяемых операций, чтобы база данных-источник могла правильно получать повторяемую операцию, когда станет резервной. Предварительное создание здесь журналов повторяемых операций позволяет автоматически создать их в резервной базе данных. Важно настроить резервные журналы повторяемых операций (SRL) с тем же размером, что и оперативные журналы повторяемых операций. Размер текущих резервных файлов журналов повторяемых операций должен в точности соответствовать размеру оперативных журналов повторяемых операций текущей базы данных-источника.

Выполните следующую инструкцию в командной строке SQL*Plus на Machine1. Представление v$logfile — это системное представление, содержащее сведения о файлах журналов повторяемых операций.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file.
	SQL> select bytes from v$log;
	BYTES
	----------
	52428800
	52428800
	52428800


Обратите внимание, что 52 428 800 байтов — это 50 МБ.

Затем в окне SQL*Plus выполните следующие инструкции, чтобы добавить новый группы файлов резервных журналов повторяемых операций и указать номера, идентифицирующие эти группы, с помощью предложения GROUP. Использование номеров групп облегчает администрирование резервных файлов журналов повторяемых операций.

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

Затем запросите следующее системное представление, чтобы просмотреть сведения о файлах журналов повторяемых операций. Эта операция также позволяет убедиться, что группы файлов резервных журналов повторяемых операций созданы.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### Включить архивирование

Теперь вы должны включить архивирование. Для этого выполните следующие инструкции, чтобы перевести базу данных-источник в режим ARCHIVELOG и включить автоматическое архивирование. Можно включить режим журнала архивирования, подключив базу данных, а затем выполнив команду archivelog.

Сначала войдите как sysdba. В командной строке Windows введите следующее:

	sqlplus /nolog

	connect / as sysdba

Затем завершите работу базы данных в командной строке SQL*Plus:

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

Затем выполните команду startup mount, чтобы подключить базу данных. В результате этой команды Oracle свяжет экземпляр с указанной базой данных.

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

Далее выполните:

	SQL> alter database archivelog;
	Database altered.

Затем выполните инструкцию Alter database с предложением Open, чтобы сделать базу данных доступной для обычного использования:

	SQL> alter database open;

	Database altered.

#### Установка параметров инициализации базы данных-источника

Чтобы настроить Data Guard, вы сначала должны создать и настроить параметры резервирования в обычном файле pfile (текстовом файле параметров инициализации). Когда pfile будет готов, необходимо преобразовать его в файл параметров сервера (файл SPFILE).

Вы можете управлять средой Data Guard с помощью параметров в файле INIT.ORA. При выполнении упражнений из этого учебника вам нужно обновить файл INIT.ORA базы данных-источника, чтобы он мог содержать обе роли: основную или резервную.

	SQL> create pfile from spfile;
	File created.

Затем необходимо изменить pfile, добавив в него параметры резервирования. Чтобы сделать это, откройте файл INITTEST.ORA в папке %ORACLE\_HOME%\\database. Затем добавьте в этот файл INITTEST.ORA следующие инструкции. Обратите внимание, что в соответствии с соглашением об именовании ваш файл INIT.ORA должен иметь имя INIT<имя\_базы\_данных>.ORA.

	db_name='TEST'
	db_unique_name='TEST'
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


Предыдущий блок инструкций содержит три важные элемента настройки: **LOG\_ARCHIVE\_CONFIG...:** с помощью этой инструкции вы задаете уникальные идентификаторы баз данных. **LOG\_ARCHIVE\_DEST\_1...:** с помощью этой инструкции вы задаете расположение папки локального архива. Рекомендуем создать новый каталог для архивирования вашей базы данных и явно указать расположение локального архива с помощью этой инструкции, а не использовать папку Oracle по умолчанию %ORACLE\_HOME%\\database\\archive. **LOG\_ARCHIVE\_DEST\_2... LGWR ASYNC...:** вы определяете процесс средства асинхронной записи в журнал (LGWR) для сбора транзакционных данных повторяемых операций и передачи их в целевые резервные расположения. Здесь DB\_UNIQUE\_NAME указывает уникальное имя для базы данных на целевом резервном сервере.

После подготовки нового файла параметров необходимо создать из него файл spfile.

Сначала завершите работу базы данных:

	SQL> shutdown immediate;

	Database closed.

	Database dismounted.

	ORACLE instance shut down.

Далее выполните команду startup nomount следующим образом:

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

Теперь создайте файл spfile:

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

	File created.

Затем завершите работу базы данных:

	SQL> shutdown immediate;

	ORA-01507: database not mounted

И запустите экземпляр с помощью команды startup:

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##Создание физической резервной базы данных
В этом разделе рассматриваются действия, которые необходимо выполнить на виртуальной машине Machine2 для подготовки физической резервной базы данных.

Во-первых, вам понадобится подключиться к удаленному рабочему столу на Machine2 с помощью классического портала Azure.

Затем на резервном сервере (Machine2) вы создадите все необходимые папки для резервной базы данных, например C:\\<локальная\_папка>\\TEST. При выполнении упражнений из этого учебника убедитесь, что структура папок на Machine2 соответствует структуре папок на Machine1 для хранения всех необходимых файлов, таких как файл параметров, файлы данных, файлы журналов повторяемых операций, а также файлы udump, bdump и cdump. Кроме того, на Machine2 должны быть определены переменные среды ORACLE\_HOME и ORACLE\_BASE. Если они не заданы, определите их как переменные среды с помощью диалогового окна «Переменные среды». Чтобы открыть это диалоговое окно, запустите служебную программу **Система**, дважды щелкнув значок "Система" на **панели управления**. Затем перейдите на вкладку **Дополнительно** и нажмите кнопку **Переменные среды**. Нажмите кнопку **Создать** в разделе **Системные переменные**, чтобы установить переменные среды. После настройки переменных среды закройте текущую командную строку Windows и откройте новую, чтобы увидеть изменения.

Затем выполните следующие действия:

1. Подготовка файла параметров инициализации для резервной базы данных.

2. Настройка прослушивателя и файла tnsnames для поддержки базы данных на основной и резервной виртуальных машинах.

	1. Настройка listener.ora на обоих серверах для хранения записей для обеих баз данных.

	2. Настройка tnsnames.ora на основной и резервной виртуальных машинах для хранения записей для базы данных-источника и резервной базы данных.

	3. Запуск прослушивателя и проверка tnsping на обеих виртуальных машин для обеих служб.

3. Запуск резервного экземпляра в состоянии nomount.

4. Использование RMAN для клонирования базы данных и создания резервной базы данных.

5. Запуск физической резервной базы данных в режиме управляемого восстановления.

6. Проверка физической резервной базы данных.

### 1\. Подготовка файла параметров инициализации для резервной базы данных

В этом разделе показывается, как подготовить файл параметров инициализации для резервной базы данных. Чтобы сделать это, сначала вручную скопируйте файл INITTEST.ORA с Machine1 на Machine2. Вы должны видеть файл INITTEST.ORA в папке %ORACLE\_HOME%\\database на обеих виртуальных машинах. Затем измените файл INITTEST.ORA на Machine2, чтобы настроить резервную роль, как показано ниже:

	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'


	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


Предыдущий блок инструкций содержит следующие два важных элемента настройки:

-	***. LOG\_ARCHIVE\_DEST\_1:** необходимо вручную создать папку c:\\OracleDatabase\\TEST\_STBY\\archives на Machine2.
-	***. LOG\_ARCHIVE\_DEST\_2:** это необязательный шаг. Он выполняется, так как это может потребоваться при обслуживании основной виртуальной машины, когда база данных на резервной виртуальной машине становится источником.

Затем необходимо запустить резервный экземпляр. На резервном сервере базы данных введите следующую команду в командной строке Windows, чтобы создать экземпляр Oracle путем создания новой службы Windows:

	oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

Обратите внимание, что команда **Oradim** создает экземпляр Oracle, но не запускает его. Его можно найти в каталоге C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN.

##Настройка прослушивателя и файла tnsnames для поддержки базы данных на основной и резервной виртуальных машинах
Перед созданием резервной базы данных убедитесь, что база данных-источник и резервная база данных в вашей конфигурации могут обмениваться данными друг с другом. Для этого необходимо настроить прослушиватель и файл TNSNames вручную или с помощью служебной программы конфигурации сети NETCA. Это обязательная задача, если вы используете служебную программу диспетчера восстановления (RMAN).

### Настройка listener.ora на обоих серверах для хранения записей для обеих баз данных

Подключитесь к удаленному рабочему столу на Machine1 и измените файл listener.ora, как указано ниже. При редактировании файла listener.ora всегда проверяйте, чтобы открывающая и закрывающая скобки стояли в одном столбце. Файл listener.ora можно найти в следующей папке: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

Затем подключитесь к удаленному рабочему столу на Machine2 и измените файл listener.ora следующим образом: # listener.ora Network Configuration File: C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\network\\admin\\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### Настройка tnsnames.ora на основной и резервной виртуальных машинах для хранения записей для базы данных-источника и резервной базы данных

Подключитесь к удаленному рабочему столу на Machine1 и измените файл tnsnames.ora, как указано ниже. Файл tnsnames.ora можно найти в следующей папке: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

Подключитесь к удаленному рабочему столу на Machine2 и измените файл tnsnames.ora следующим образом:

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### Запуск прослушивателя и проверка tnsping на обеих виртуальных машин для обеих служб

Откройте новую командную строку Windows на основной и резервной виртуальных машинах и выполните следующие инструкции:

	C:\Users\DBAdmin>tnsping test

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)


	C:\Users\DBAdmin>tnsping test_stby

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##Запуск резервного экземпляра в состоянии nomount
Вам нужно настроить среду для поддержки резервной базы данных на резервной виртуальной машине (MACHINE2).

Сначала вручную скопируйте файл пароля с основной виртуальной машины (Machine1) на резервную (Machine2). Это необходимо, так как пароль **sys** должен быть одинаковым на обеих машинах.

Затем откройте командную строку Windows на Machine2 и настройте переменные среды, чтобы они указывали на резервную базу данных:

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

Теперь запустите резервную базу данных в состоянии nomount и создайте файл spfile.

Запустите базу данных:

	SQL>shutdown immediate;

	SQL>startup nomount
	ORACLE instance started.

	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##Использование RMAN для клонирования базы данных и создания резервной базы данных
С помощью служебной программы диспетчера восстановления (RMAN) вы можете сделать резервную копию базы данных-источника, чтобы создать физическую резервную базу данных.

Подключитесь к удаленному рабочему столу на резервной виртуальной машине (Machine2) и запустите служебную программу RMAN, указав полную строку подключения для экземпляров TARGET (база данных-источник, Machine1) и AUXILLARY (резервная база данных, Machine2).

>[AZURE.IMPORTANT]Не используйте аутентификацию операционной системы, так как на резервном сервере еще нет базы данных.

	C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##Запуск физической резервной базы данных в режиме управляемого восстановления
В этом учебнике демонстрируется создание физической резервной базы данных. Информацию о создании логической резервной базы данных см. в документации по Oracle.

Откройте командную строку SQL*Plus и включите Data Guard на резервной виртуальной машине или сервере (MACHINE2) следующим образом:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Когда вы открываете резервную базу данных в режиме **MOUNT**, продолжается доставка журналов архивирования, и процесс управляемого восстановления продолжает применять журналы в резервной базе данных. Это обеспечивает поддержку резервной базы данных в актуальном состоянии по отношению к базе данных-источнику. Обратите внимание, что в течение этого времени резервная база данных недоступна для составления отчетов.

Когда вы открываете резервную базу данных в режиме **READ ONLY**, доставка журналов архивирования продолжается. Но процесс управляемого восстановления останавливается. В результате резервная база данных все более устаревает, до возобновления процесса управляемого восстановления. В это время можно использовать резервную базу данных для составления отчетов, но данные могут не отражать последние изменения.

В целом мы рекомендуем установить режим **MOUNT**, чтобы обеспечить актуальность данных в резервной базе данных в случае сбоя базы данных-источника. Но вы также можете установить режим **READ ONLY** для составления отчетов, в зависимости от требований вашего приложения. Следующие шаги демонстрируют включение Data Guard в режиме только для чтения с помощью SQL*Plus.

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##Проверка физической резервной базы данных
В этом разделе показывается, как проверить конфигурацию высокой доступности от имени администратора.

Откройте окно командной строки SQL*Plus и проверьте архивные журналы повторяемых операций на резервной виртуальной машине (Machine2):

	SQL> show parameters db_unique_name;

	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY

	SQL> SELECT NAME FROM V$DATABASE

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

Откройте окно командной строки SQL*Plus и переключите файлы журналов на основную виртуальную машину (Machine1):

	SQL> alter system switch logfile;
	System altered.

	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

Проверьте архивные журналы повторяемых операций на резервной виртуальной машине (Machine2):

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES

	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

Проверьте наличие пропусков на резервной виртуальной машине (Machine2):

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

Другой способ проверки заключается в том, чтобы выполнить отработку отказа на резервную базу данных, а затем проверить, можно ли восстановить размещение в базе данных-источнике: Чтобы задействовать резервную базу данных в качестве базы данных-источника, используйте следующие инструкции:

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Если вы не включили переключение обратно в исходную базу данных-источник, рекомендуется удалить исходную базу данных-источник и воссоздать ее как резервную базу данных.

Рекомендуется включить переключение базы данных в базе данных-источнике и в резервной базе данных. В случае отказа базу данных-источник можно будет переключить обратно на момент до отказа и быстро преобразовать в резервную базу данных.

##Дополнительные ресурсы
[Образы виртуальных машин Oracle для Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=AcomDC_1203_2015-->