<properties
	pageTitle="Настройка Oracle GoldenGate на виртуальных машинах | Microsoft Azure"
	description="Изучите учебник по установке и реализации Oracle GoldenGate на виртуальных машинах Azure с Windows Server для обеспечения высокого уровня доступности и аварийного восстановления."
	services="virtual-machines-windows"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />


#Настройка Oracle GoldenGate для Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


В этом учебнике показано, как настроить Oracle GoldenGate для среды виртуальных машин Azure, чтобы обеспечить высокий уровень доступности и аварийное восстановление. Учебник посвящен [двунаправленной репликации](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) баз данных Oracle, не являющихся кластерами реальных приложений (RAC), и для него требуется, чтобы оба сайта были активны.

Oracle GoldenGate поддерживает интеграцию данных и распределение данных. Это позволяет настроить решение для распределения данных и синхронизации данных с помощью конфигурации репликации из Oracle в Oracle, а также предоставляет гибкое решение, обеспечивающее высокий уровень доступности. Oracle GoldenGate дополняет Oracle Data Guard своими возможностями репликации, чтобы обеспечить распространение информации по всему предприятию, а также отсутствие простоев при обновлении и миграции. Дополнительные сведения см. в разделе [Использование Oracle GoldenGate с Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate состоит из следующих основных компонентов: Extract, Data Pump, Replicat, Trails (или извлекаемые файлы), Checkpoints, Manager и Collector. Для двунаправленной репликации между двумя сайтами необходимо создать и запустить все эти компоненты на обоих сайтах. Дополнительные сведения об архитектуре Oracle GoldenGate см. в [руководстве по Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

В этом учебнике предполагается, что вы уже ознакомились с концепциями высокого уровня доступности и аварийного восстановления баз данных Oracle, а также [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html), как в теории, так и на практике. Дополнительные сведения см. на [веб-сайте Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Кроме того, для работы с учебником вы должны выполнить следующие предварительные требования:

- Ознакомиться с подразделом "Рекомендации по высокой доступности и аварийному восстановлению" раздела [Различные рекомендации по образам виртуальных машин Oracle](virtual-machines-windows-classic-oracle-considerations.md). Обратите внимание, что в настоящее время Azure поддерживает автономные экземпляры базы данных Oracle, но не кластеры реальных приложений Oracle (Oracle RAC).

- Скачать программное обеспечение Oracle GoldenGate с веб-сайта [Oracle](http://www.oracle.com/us/downloads/index.html). Необходимо выбрать Product Pack Oracle Fusion Middleware – Data Integration. Затем следует выбрать пакет Oracle GoldenGate on Oracle v11.2.1 Media Pack для Microsoft Windows x64 (64-разрядная версия) для базы данных Oracle 11g. После этого следует скачать Oracle GoldenGate V11.2.1.0.3 для Oracle 11g (64-разрядная версия) для Windows 2008 (64-разрядная версия).

- Создать две виртуальные машины в Azure с помощью платформы, которая предоставляется образом Oracle Enterprise Edition в Windows Server. Информацию см. в статьях [Создание виртуальной машины Oracle Database 12c в Azure](#z3dc8d3c097cf414e9048f7a89c026f80) и [Виртуальные машины Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Убедиться, что эти виртуальные машины находятся в [одной облачной службе](virtual-machines-linux-load-balance.md) и [одной виртуальной сети](https://azure.microsoft.com/documentation/services/virtual-network/), чтобы они могли обращаться друг к другу посредством постоянного частного IP-адреса.

- На классическом портале Azure присвоить виртуальным машинам имена: MachineGG1 для сайта A и MachineGG2 для сайта B.

- Создать тестовые базы данных TestGG1 на сайте A и TestGG2 на сайте B.

- Войти на сервер Windows как участник группы Administrators или **ORA\_DBA**.

Изучив данный учебник, вы научитесь:

1. Настраивать базу данных на сайтах A и B:  

	1. Выполнять начальную загрузку данных.

2. Подготавливать сайты А и B для репликации базы данных.

3. Создавать все необходимые объекты для поддержки репликации DDL.

4. Настраивать GoldenGate Manager на сайтах А и B.

5. Создавать процессы Extract Group и Data Pump на сайтах A и B:

	1. Создавать процессы Extract и Data Pump на сайте A.

	2. Создавать таблицу контрольных точек GoldenGate на сайте B.

	3. Добавлять REPLICAT на сайт B.

	4. Создавать процессы Extract и Data Pump на сайте B.

	5. Создавать таблицу контрольных точек GoldenGate на сайте A.

	6. Добавлять REPLICAT на сайт A.

	7. Выполнять команду ADD TRANDATA для сайтов A и B.

	8. Запускать процессы Extract и Data Pump на сайте A.

	9. Запускать процессы Extract и Data Pump на сайте B.

	10. Запускать процесс REPLICAT на сайте A.

	11. Запускать процесс REPLICAT на сайте B.

6. Проверять процесс двунаправленной репликации.

>[AZURE.IMPORTANT] Данный учебник был разработан и протестирован для следующей конфигурации программного обеспечения.
>
>| | **База данных сайта A** | **База данных сайта B** |
>|------------------------|----------------------------------|----------------------------------|
>| **Версия Oracle** | Oracle11g Release 2 – (11.2.0.1) | Oracle11g Release 2 – (11.2.0.1) |
>| **Имя компьютера** | MachineGG1 | MachineGG2 |
>| **Операционная система** | Windows 2008 R2 | Windows 2008 R2 |
>| **ИД безопасности Oracle** | TESTGG1 | TESTGG2 |
>| **Схема репликации** | SCOTT | SCOTT |

Для последующих выпусков базы данных Oracle и Oracle GoldenGate может потребоваться реализовать некоторые дополнительные изменения. Последние сведения о версиях см. в документации по [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) и [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) на веб-сайте Oracle. Например, для базы данных-источника версии 11.2.0.4 и более поздних версий запись DDL выполняется асинхронно сервером интеллектуального анализа журналов и не требует установки специальных триггеров, таблиц или других объектов базы. Обновления Oracle GoldenGate могут выполняться без остановки приложений пользователя. Использование триггера DDL и вспомогательных объектов необходимо, когда Extract выполняется в режиме интеграции с базой данных-источником Oracle 11g, версия которой предшествует 11.2.0.4. Подробные инструкции см. в руководстве [Установка и настройка Oracle GoldenGate для базы данных Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##1\. Настройка базы данных на сайтах A и B
В этом разделе объясняется, как выполнять предварительные требования к базе данных на сайтах А и B. Все шаги этого раздела необходимо выполнить на обоих сайтах, А и B.

Сначала установите подключение удаленного рабочего стола для сайта А и сайта B на классическом портале Azure. Откройте командную строку Windows и создайте домашний каталог для файлов установки Oracle GoldenGate:

	mkdir C:\OracleGG

Затем распакуйте и установите программное обеспечение Oracle GoldenGate в эту папке. После выполнения этого шага можно инициировать интерпретатор программных команд GoldenGate (GGSCI), выполнив следующую команду:

	C:\OracleGG\.\ggsci

С помощью [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) можно выполнить несколько команд для настройки, управления и мониторинга Oracle GoldenGate.

Далее, выполните следующую команду, чтобы создать все вложенные папки из пакета установки:

	GGSCI (Hostname) 1> CREATE SUBDIRS

Выполните следующую команду для выхода из командной строки GGSCI:

	GGSCI (Hostname) 1> EXIT

Затем необходимо создать пользователя базы данных, который будет использоваться процессами Manager, Extract и Replication Oracle GoldenGate. Обратите внимание, что можно создать отдельных пользователей для каждого процесса или настроить только одного общего пользователя. В этом учебнике мы создадим одного пользователя, ggate. Затем мы предоставим этому пользователю необходимые привилегии. Обратите внимание, что следующие операции необходимо выполнить на сайте А и сайте B.

Откройте командное окно SQL*Plus на сайте A и сайте B с правами администратора базы данных, используя **SYSDBA**, например:

	Enter username: / as sysdba

И выполните:

	SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata<DBNAME><DBNAME>ggs_data01.dbf' size 200m;
	SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
	      grant connect, resource to ggate;
	      grant select any dictionary, select any table to ggate;
	      grant create table to ggate;
	      grant flashback any table to ggate;
	      grant execute on dbms_flashback to ggate;
	      grant execute on utl_file to ggate;
	      grant create any table to ggate;
	      grant insert any table to ggate;
	      grant update any table to ggate;
	      grant delete any table to ggate;
	      grant drop any table to ggate;

Затем найдите файл INIT<ИД\_безопасности\_базы\_данных>.ORA в папке %ORACLE\_HOME%\\database на сайте A и сайте B и добавьте следующие параметры базы данных в INITTEST.ora:

	UNDO\_MANAGEMENT=AUTO
	UNDO\_RETENTION=86400

Полный список всех команд Oracle GoldenGate GGSCI см. в [справочнике по Oracle GoldenGate для Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### Выполнение начальной загрузки данных

Начальную загрузку данных в базу данных можно выполнить несколькими методами. Например, экспортировать данные таблицы с сайта А на сайт B можно с помощью [прямой загрузки Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) или обычных служебных программ для экспорта и импорта.

Для демонстрации процесса репликации Oracle GoldenGate в этом учебнике показано создание таблицы на обоих сайтах A и B с помощью следующих команд.

Сначала откройте командное окно SQL*Plus и выполните следующую команду, чтобы создать таблицу inventory в базах данных на сайтах A и B:

	create table scott.inventory
	(prod_id number,
	prod_category varchar2(20),
	qty_in_stock number,
	last_dml timestamp default systimestamp);

Затем добавьте ограничение для новой таблицы, созданной в базах данных на сайтах A и B:

	alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Предоставьте пользователю ggate все привилегии для таблицы inventory на сайтах А и B:

	grant all on scott.inventory to ggate;

После этого создайте и включите триггер базы данных INVENTORY\_CDR\_TRG для созданной таблицы, чтобы обеспечить запись всех транзакций в новую таблицу, если пользователь — не ggate. Выполните эту операцию на сайтах А и B.

	CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
	BEFORE UPDATE
	ON SCOTT.INVENTORY
	REFERENCING NEW AS New OLD AS Old
	FOR EACH ROW
	BEGIN
	IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
	THEN
	:NEW.LAST_DML := SYSTIMESTAMP;
	END IF;
	END;
	/


##2\. Подготовка сайтов А и B для репликации базы данных
В этом разделе поясняется, как подготовить сайты А и B для репликации базы данных. Все шаги этого раздела необходимо выполнить на обоих сайтах, А и B.

Сначала установите подключение удаленного рабочего стола для сайта А и сайта B на классическом портале Azure. Переключите базу данных в режим archivelog с помощью командного окна SQL*Plus:

	sql>shutdown immediate
	sql>startup mount
	sql>alter database archivelog;
	sql>alter database open;


Затем включите минимальное [дополнительное ведение журнала](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) следующим образом:

	SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Затем подготовьте базу данных для поддержки репликации DDL (язык описания данных):

	SQL> alter system set recyclebin=off scope=spfile;

Завершите работу базы данных и перезапустите ее:

	sql>shutdown immediate
	sql>startup


##3\. Создание всех необходимых объектов для поддержки репликации DDL
В этом разделе перечислены сценарии, которые надо использовать для создания всех объектов, необходимых для поддержки репликации DDL. Сценарии, указанные в этом разделе, необходимо выполнить на сайте А и сайте B.

Откройте командную строку Windows и перейдите к папке Oracle GoldenGate, например C:\\OracleGG. Откройте командную строку SQL*Plus на сайте A и сайте B с правами администратора базы данных, например, используя **SYSDBA**.

Затем выполните следующие сценарии:

	SQL> @marker_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @ddl_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @role_setup.sql
	Enter GoldenGate schema name: ggate
	SQL> grant ggs_ggsuser_role to ggate;
	 Grant succeeded.
	 SQL> @ddl_enable
	 Trigger altered.
	 SQL> @ddl_pin ggate


Для поддержки DDL (языка описания данных) инструменту Oracle GoldenGate требуется вход на уровне таблицы. Поэтому включите дополнительное ведение журнала на уровне таблицы с помощью команды ADD TRANDATA. Откройте окно интерпретатора команд GoldenGate Oracle, войдите в базу данных и выполните команду ADD TRANDATA:

	GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

	GGSCI(Hostname) 6> add trandata scott.inventory

##4\. Настройка GoldenGate Manager на сайтах А и B
Диспетчер Oracle GoldenGate Manager выполняет несколько функций, например: запуск других процессов GoldenGate, управление файлами журнала следов и составление отчетов.

Необходимо настроить процесс Oracle GoldenGate Manager на сайтах А и B. Для этого выполните следующие действия на сайтах А и B.

Откройте командное окно Windows и инициируйте интерпретатор команд Oracle GoldenGate:

	cd C:\OracleGG\
	c:\OracleGG>ggsci
	Oracle GoldenGate Command Interpreter for Oracle
	Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
	Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
	Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Зарегистрируйте сеанс GGSCI в базе данных, чтобы можно было выполнять команды, влияющие на базу данных:

	GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

Отобразите состояние и запаздывание (где необходимо) для всех процессов Manager, Extract и Replicat в системе:

	GGSCI (HostName) 2> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Откройте файл параметров с помощью команды EDIT PARAMS и добавьте в него следующую информацию:

	GGSCI (HostName) 3> edit params mgr
	PORT 7809
	USERID ggate, PASSWORD ggate
	PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Отобразите состояние и запаздывание (где необходимо) для всех процессов Manager, Extract и Replicat в системе:

	GGSCI (HostName) 46> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Зарегистрируйте сеанс GGSCI в базе данных, чтобы можно было выполнять команды, влияющие на базу данных:

	GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

	Successfully logged into database.

Запустите процесс Manager:

	GGSCI (HostName) 48> start manager
	Manager started.

##5\. Создание процессов Extract Group и Data Pump на сайтах A и B

###Создавать процессы Extract и Data Pump на сайте A.

Необходимо создать процессы Extract и Data Pump на сайтах A и B. Установите подключение удаленного рабочего стола к сайтам А и B на классическом портале Azure. Откройте окно интерпретатора команд GGSCI. Выполните следующие команды на сайте A:

	GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
	EXTRACT added.
	GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
	EXTTRAIL added.
	GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
	EXTRACT added.
	GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
	RMTTRAIL added.

Откройте файл параметров с помощью команды EDIT PARAMS и добавьте в него следующую информацию: GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\\OracleGG\\dirdat\\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml), ON DELETE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml));

Откройте файл параметров с помощью команды EDIT PARAMS и добавьте в него следующую информацию:

	GGSCI (MachineGG1) 15> edit params dpump1
	EXTRACT dpump1
	 USERID ggate, PASSWORD ggate
	 RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
	 RMTTRAIL C:\OracleGG\dirdat\ab
	 PASSTHRU
	 TABLE scott.inventory;

###Создание таблицы контрольных точек GoldenGate на сайте B

Далее необходимо добавить таблицу контрольных точек на сайт B. Для этого необходимо открыть окно интерпретатора команд GoldenGate и выполнить:

	C:\OracleGG\ggsci
	GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

После этого добавьте таблицу контрольных точек в базу данных, владельцем которой является ggate:

	GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
	Successfully created checkpoint table ggate.checkpointtable.

Добавьте имя таблицы контрольных точек в файл GLOBALS на целевом сервере, которым в данном случае является сайт B. Измените файл GLOBALS на сайте B:

	GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Затем добавьте параметр CHECKPOINTTABLE в существующий файл GLOBALS:

	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Наконец, сохраните и закройте файл параметров GLOBALS.


###Добавление REPLICAT на сайт B
В этом разделе описывается добавление процесса REPLICAT REP2 на сайт B.

С помощью команды ADD REPLICAT создайте группу Replicat на сайте B:

	GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Откройте файл параметров с помощью команды EDIT PARAMS и добавьте в него следующую информацию:

	GGSCI (MachineGG2) 10> edit params rep2
	REPLICAT rep2
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

###Создание процессов Extract и Data Pump на сайте B

В этом разделе описывается создание нового процесса Extract EXT2 и нового процесса Data Pump DPUMP2 на сайте B:

	GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
	 EXTRACT added.
	GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
	 EXTTRAIL added.
	GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
	 EXTRACT added.
	GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
	 RMTTRAIL added.

Откройте файл параметров с помощью команды EDIT PARAMS и добавьте в него следующую информацию:

	GGSCI (MachineGG2) 31> edit params ext2
	EXTRACT ext2
	USERID ggate, PASSWORD ggate
	EXTTRAIL C:\OracleGG\dirdat\ac
	TRANLOGOPTIONS EXCLUDEUSER ggate
	TABLE scott.inventory,
	GETBEFORECOLS (
	ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
	ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Откройте файл параметров с помощью команды EDIT PARAMS и добавьте в него следующую информацию:

	GGSCI (MachineGG2) 32> edit params dpump2
	EXTRACT dpump2
	USERID ggate, PASSWORD ggate
	RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
	RMTTRAIL C:\OracleGG\dirdat\ad
	PASSTHRU
	TABLE scott.inventory;

###Создание таблицы контрольных точек GoldenGate на сайте A

Откройте окно интерпретатора команд Oracle GoldenGate и создайте таблицу контрольных точек:

	GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

	GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

	Successfully created checkpoint table ggate.checkpointtable.

Также необходимо добавить имя таблицы контрольных точек в файл GLOBALS на сайте A.

Откройте окно интерпретатора команд Oracle GoldenGate и измените файл GLOBALS на сайте A:

	GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
	Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Сохраните и закройте файл параметров GLOBALS.

###Добавление REPLICAT на сайт A

В этом разделе описывается добавление процесса REPLICAT REP1 на сайт A.

Следующая команда создает группу Replicat rep1 с именем следа и связанной таблицей контрольных точек.

	GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
	 REPLICAT added.

Откройте файл параметров с помощью команды EDIT PARAMS и добавьте в него следующую информацию:

	GGSCI (MachineGG1) 10> edit params rep1
	REPLICAT rep1
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

### Выполнение команды ADD TRANDATA для сайтов A и B

Включите дополнительное ведение журнала на уровне таблицы с помощью команды ADD TRANDATA. Откройте окно интерпретатора команд GoldenGate Oracle, войдите в базу данных и выполните команду ADD TRANDATA.

Установите подключение удаленного рабочего стола к MachineGG1, откройте интерпретатор команд Oracle GoldenGate и выполните:

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Установите подключение удаленного рабочего стола к MachineGG2, откройте интерпретатор команд Oracle GoldenGate и выполните:

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Выведите информацию о состоянии дополнительного ведения журнала на уровне таблицы:

	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Выполнение команды ADD TRANDATA для сайтов A и B

Включите дополнительное ведение журнала на уровне таблицы с помощью команды ADD TRANDATA. Откройте окно интерпретатора команд GoldenGate Oracle, войдите в базу данных и выполните команду ADD TRANDATA.

Установите подключение удаленного рабочего стола к MachineGG1, откройте интерпретатор команд Oracle GoldenGate и выполните:

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Установите подключение удаленного рабочего стола к MachineGG2, откройте интерпретатор команд Oracle GoldenGate и выполните:

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
	Display information about the state of table-level supplemental logging:
	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Запуск процессов Extract и Data Pump на сайте A

Запустите процесс Extract ext1 на сайте A:

	GGSCI (MachineGG1) 31> start extract ext1
	Sending START request to MANAGER …
	EXTRACT EXT1 starting

Запустите процесс Data Pump dpump1 на сайте A:

	GGSCI (MachineGG1) 23> start extract dpump1
	Sending START request to MANAGER …
	EXTRACT DPUMP1 starting
Выведите информацию о группе Extract ext1: GGSCI (MachineGG1) 32> info extract ext1 EXTRACT EXT1 Last Started 2013-11-25 08:03 Status RUNNING Checkpoint Lag 00:00:00 (updated 00:00:02 ago) Log Read Checkpoint Oracle Redo Logs 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Отобразите состояние и запаздывание (где необходимо) для всех процессов Manager, Extract и Replicat в системе:

	GGSCI (MachineGG1) 16> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
	EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###Запуск процессов Extract и Data Pump на сайте B

Запустите процесс Extract ext2 на сайте B:

	GGSCI (MachineGG2) 22> start extract ext2
	Sending START request to MANAGER …
	EXTRACT EXT2 starting

Запустите процесс Data Pump dpump2 на сайте B:

	GGSCI (MachineGG2) 23> start extract dpump2
	Sending START request to MANAGER …
	EXTRACT DPUMP2 starting

Отобразите состояние и запаздывание (где необходимо) для всех процессов Manager, Extract и Replicat в системе:

	GGSCI (ActiveGG2orcldb) 6> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
	EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### Запуск процесса REPLICAT на сайте A

В этом разделе описывается запуск процесса REPLICAT REP1 на сайте A.

Запустите процесс REPLICAT на сайте A:

	GGSCI (MachineGG1) 38> start replicat rep1
	Sending START request to MANAGER …
	REPLICAT REP1 starting

Выведите состояние группы REPLICAT:

	GGSCI (MachineGG1) 39> status replicat rep1
	 REPLICAT REP1: RUNNING

###Запуск процесса REPLICAT на сайте B

В этом разделе описывается запуск процесса REPLICAT REP2 на сайте B.

Запустите процесс REPLICAT на сайте B:

	GGSCI (MachineGG2) 26> start replicat rep2
	Sending START request to MANAGER …
	REPLICAT REP2 starting

Выведите состояние группы REPLICAT:

	GGSCI (MachineGG2) 27> status replicat rep2
	REPLICAT REP2: RUNNING

##6\. Проверка процесса двунаправленной репликации

Чтобы проверить конфигурацию Oracle GoldenGate, вставьте строку в базу данных на сайте A. Установите подключение удаленного рабочего стола к сайту A. Откройте командное окно SQL*Plus и выполните: SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> insert into inventory values  (100,’TV’,100,sysdate);

	1 row created.

	SQL> commit;

	Commit complete.

Затем проверьте, реплицирована ли эта строка на сайт B. Для этого установите подключение удаленного рабочего стола к сайту B. Откройте окно SQL Plus и выполните:

	SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13

Вставьте новую запись на сайте B:

	SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
	1 row created.

	SQL> commit;

	Commit complete.

Установите подключение удаленного рабочего стола к сайту А и проверьте, была выполнена репликация:

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13
	101 DVD 10 22-MAR-13

##Дополнительные ресурсы
[Образы виртуальных машин Oracle для Azure](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0323_2016-->