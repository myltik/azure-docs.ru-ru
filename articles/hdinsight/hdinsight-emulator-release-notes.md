<properties 
	pageTitle="Заметки о выпуске: эмулятор Microsoft HDInsight для Azure | Microsoft Azure" 
	description="Получите самую свежую информацию о новейших версиях эмулятора Hadoop в HDInsight, изолированной среды Hadoop." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="jgao"/>




# Заметки о выпуске: эмулятор Microsoft HDInsight для Hadoop 



> [AZURE.NOTE]Самый простой способ проверить номер версии заключается в том, чтобы найти запись **Эмулятор Microsoft HDInsight для Azure** (для версии 1.0.0.0 или более поздней версии) или **Microsoft HDInsight Developer Preview** (для версий младше 1.0.0.0) в окне «Установка и удаление программ».

## Версия 2.0.0.0, вышедшая 29 августа 2014 г.

* Данная версия содержит обновления эмулятора HDInsight и предназначена для проектов Hadoop, в настоящее время используемых в версии 3.1.    

* Как и в случае с предварительными выпусками этого продукта, данный выпуск ориентирован на сценарии разработчиков и поэтому поддерживает только развертывания на одном узле.

### Новые возможности 
 
* [Обновленные версии компонентов Hadoop](hdinsight-component-versioning.md), соответствующие версии 3.1 данной службы. Сюда входит поддержка Hive версии 0.13 и Tez.

## Версия 1.0.0.0, вышедшая 28 октября 2013 г.

* Это общедоступный (GA) выпуск эмулятора Microsoft HDInsight для Azure, ранее известный как Microsoft HDInsight Developer Preview. 

* Как и в случае с предварительными выпусками этого продукта, данный выпуск ориентирован на сценарии разработчиков и поэтому поддерживает только развертывания на одном узле.

### Новые возможности 
 
* Были добавлены скрипты для упрощения настройки всех служб Apache Hadoop в режим автоматического или ручного запуска. По умолчанию по-прежнему используется автоматический режим, но теперь можно изменить все службы с помощью скриптов set-onebox-manualstart.cmd или set-onebox-autostart.cmd, установленных в папку C:\\Hadoop. 

* Число требуемых зависимостей установки было существенно сокращено, что позволяет ускорить установку.

* Эта версия включает исправление ошибки в команде, используемой для запуска примеров Pig в скрипте RunSamples.ps1, установленном в папку GettingStarted.

* Эта версия содержит обновление для платформы данных Hortonworks Data Platform (HDP) версии 1.1, которая соответствует службам Hortonworks Data Platform, доступным в кластере Azure HDInsight версии 1.6.

## Версия 0.11.0.0, вышедшая 30 сентября 2013 г.

### Новые возможности 
		
* Панель мониторинга HDInsight была удалена. 

* Эта версия содержит обновление для платформы данных Hortonworks Data Platform Developer Preview, которая соответствует платформе Hortonworks Data Platform Preview в Azure HDInsight.

## Версия 0.10.0.0, вышедшая 9 августа 2013 г.

### Новые возможности 
	
* Эта версия содержит обновление для платформы данных Hortonworks Data Platform Developer Preview, которая соответствует платформе Hortonworks Data Platform Preview в Azure HDInsight.

## Версия 0.8.0.0, вышедшая 7 июня 2013 г.

### Новые возможности 

* Эта версия содержит обновление для платформы данных Hortonworks Data Platform Developer Preview, которая соответствует платформе Hortonworks Data Platform Preview в Azure HDInsight.

## Версия 0.6.0.0, вышедшая 13 мая 2013 г.

### Новые возможности 

* Теперь устанавливается Hive Server 2. Это необходимо для версии 0.9.2.0 драйвера Microsoft ODBC Driver для Hive, выпущенной в одно время с данным обновлением. 

* Все службы настроены на автоматический запуск, поэтому после перезагрузки компьютера не требуется повторно запускать каждый из компонентов.

## Версия 0.4.0.0, вышедшая 25 марта 2013 г.

### Новые возможности 

* Новый выпуск Microsoft HDInsight Developer Preview, а также платформы данных Hortonworks Data Platform для Windows Developer Preview. 

* Включает в себя Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog и Templeton.

* Новая панель мониторинга HDInsight со следующими возможностями.
 
	* Подключение к нескольким кластерам, включая локальные установки, а также установки, выполняемые удаленно с помощью службы Azure HDInsight. Дополнительные сведения о службе HDInsight см. по адресу [http://azure.microsoft.com/documentation/services/hdinsight/](http://azure.microsoft.com/documentation/services/hdinsight/).
 
	* Настройка хранилища больших двоичных объектов Azure в локальном кластере. Ниже приведены подробные инструкции.

	* Создавайте и редактируйте запросы Hive в новой интерактивной консоли Hive.

	* Просматривайте и загружайте журнал заданий и результаты.

### Заметки о выпуске 

* Доступ к конечным точкам REST API в локальной установке HDInsight и службе Azure HDInsight осуществляется через различные номера портов для одной службы. 

	Локально: Oozie — http://localhost:11000/oozie/v1/admin/status Templeton — http://localhost:50111/templeton/v1/status ODBC: используйте порт 10000 в конфигурации DSN или строке подключения.

	Служба HDInsight: Oozie — http://ServerFQDN:563/oozie/v1/admin/status Templeton — http://ServerFQDN:563/templeton/v1/status ODBC: используйте порт 563 в конфигурации DSN или строке подключения.


* Настройка хранилища больших двоичных объектов Azure в локальном кластере.

	В панели мониторинга отображается локальный кластер по умолчанию с именем "local (hdfs)". Если требуется использовать хранилище больших двоичных объектов Azure в качестве хранилища для локальной установки, выполните следующие действия.

	1. Добавьте тег учетной записи в core-site.xml в папке C::\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf:       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		Пример:

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. Откройте командную оболочку Hadoop на рабочем столе в режиме с повышенными правами и выполните следующие команды:
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. Обратитесь к любому файлу в этой учетной записи, используя полный URI: asv://{container}@{account}/{path} (или asvs://, если вы хотите использовать HTTPS для доступа к данным). Пример:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Удалите зарегистрированный в настоящее время локальный кластер и повторно зарегистрируйте его с использованием новых учетных данных хранилища больших двоичных объектов Azure.


## Версия 0.3.0.0, вышедшая 13 декабря 2012 г. 

* Веб-сайт панели мониторинга был переведен на анонимную проверку подлинности вместо использования учетных данных Windows. Это устраняет проблему с запросом входа, которая упоминается в заметках о выпуске для предыдущей версии. 

* Было исправлено несколько ошибок Sqoop, возникающих при экспорте и некоторых типах импорта.

### Проблемы выпуска 

* Не удается загрузить консоль JavaScript. Подробности см. в заметках о выпуске для версии 0.2.0.0. 
* Командная строка Sqoop будет отображать предупреждения, как показано ниже. Это будет исправлено в следующем обновлении, поэтому данную проблему можно спокойно игнорировать. 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## Версия 0.2.0.0, вышедшая 3 декабря 2012 г.

* Введение в семантическое управление версиями в установщике Windows. 

* Исправления различных ошибок установки, о которых сообщалось на форумах MSDN, в частности связанных с установкой панели мониторинга HDInsight.

* Добавлены элементы в меню **Пуск** для ускорения доступа к ним.

* Исправление проблемы с многострочным вводом в консоли Hive.

* Незначительные изменения в материалах по началу работы.

### Проблемы выпуска 

* Не удается загрузить консоль JavaScript. 

	* В некоторых установках происходит сбой консоли JavaScript с отображением на странице ошибки HTTP 404. Чтобы обойти это, для работы с консолью перейдите непосредственно к адресу http://localhost:8080. 

* Переход к панели мониторинга HDInsight вызывает отображение окна входа в систему.

	* Мы получили несколько отчетов о том, что при переходе к панели мониторинга HDInsight отображается диалоговое окно для входа. В этом случае можно указать сведения для входа текущего пользователя, после чего вы должны получить возможность перейти к панели мониторинга. 


## Версия 1.0.0.0, вышедшая 23 октября 2012 г.

* Первый выпуск 

### Проблемы выпуска 

* Консоль Hive 

	* Если в отправленную команду Hive включен символ новой строки, вы получите синтаксическую ошибку. Удалите символы новой строки, и запрос будет выполняться правильно. 



## Общие известные проблемы

* Истечение срока действия пароля пользователя Hadoop 

	Срок действия пароля пользователя Hadoop может истечь в зависимости от политик Active Directory, размещенных на компьютере. Следующий скрипт Windows PowerShell задает бессрочный пароль; его можно выполнить из административной командной строки.

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* Каталог временных данных
	
	Файл hadoop.tmp.dir указывает на неверное расположение. вместо C:\\hadoop\\hdfs, он указывает на c:\\hdfs. Эта ошибка будет исправлена в следующем обновлении битов HDP.

* Ограничения операционной системы

	Сервер HDInsight следует устанавливать в 64-разрядной ОС.

* Результаты поиска Web Platform Installer (WebPI)

	HDInsight не удается найти в результатах поиска WebPI. Обычно это вызвано ограничениями операционной системы. HDInsight требуется 64-разрядная версия операционной системы не ниже Windows 7 с пакетом обновления 1, Windows Server 2008 R2 с пакетом обновления 1, Windows 8 или Windows Server 2012.

* Документация по административной командной строке

	* Такие команды, как **hadoop mradmin** или **hadoop defadmin**, следует запускать в качестве пользователя Hadoop. 

	* Для создания оболочки, которая работает в качестве такого пользователя, откройте командную строку Hadoop и введите следующее:
	 
	 		start-hadoopadminshell

	* Это откроет новую командную оболочку с правами администратора Hadoop.

##<a name="nextsteps"></a> Дальнейшие действия

- [Приступая к работе с эмулятором HDInsight][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: ../hdinsight-get-started-emulator.md

 

<!---HONumber=August15_HO6-->