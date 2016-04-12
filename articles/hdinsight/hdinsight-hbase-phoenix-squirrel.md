<properties 
   pageTitle="Использование Apache Phoenix и SQuirreL в HDInsight | Microsoft Azure" 
   description="Узнайте о том, как использовать Apache Phoenix в HDInsight, а также как установить и настроить SQuirreL на рабочей станции для подключения к кластеру HBase в HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/04/2016"
   ms.author="jgao"/>

# Использование Apache Phoenix и SQuirreL с кластерами HBase под управлением Windows в HDinsight  

Узнайте о том, как использовать [Apache Phoenix](http://phoenix.apache.org/) в HDInsight, а также как установить и настроить SQuirreL на рабочей станции для подключения к кластеру HBase в HDInsight. Дополнительные сведения о Phoenix см. в разделе [Phoenix за 15 минут или меньше](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Для информации по грамматике Phoenix обратитесь к разделу [Грамматика Phoenix](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Для получения информации о версии Phoenix в HDInsight см. раздел [Новые возможности версий кластера Hadoop, предоставляемых HDInsight][hdinsight-versions].
>
> Информация, приведенная в этом документе, относится только к кластерам HDInsight под управлением Windows. Сведения об использовании Phoenix в HDInsight под управлением Linux см. в разделе [Использование Apache Phoenix с кластерами HBase под управлением Linux в HDinsight](hdinsight-hbase-phoenix-squirrel-linux.md).

##Использование SQLLine
[SQLLine](http://sqlline.sourceforge.net/) — это утилита командной строки для выполнения SQL.

###Предварительные требования
Перед использованием SQLLine необходимо иметь следующее:

- **Кластер HBase в HDInsight**. Сведения о подготовке кластера HBase см. в разделе [Начало работы с Apache HBase в HDInsight][hdinsight-hbase-get-started].
- **Подключение к кластеру с помощью протокола удаленного рабочего стола**. Инструкции см. в статье [Управление кластерами Hadoop в HDInsight с помощью классического портала Azure][hdinsight-manage-portal].

**Определение имени узла**

1. Откройте **командную строку Hadoop** с рабочего стола.
2. Выполните следующую команду, чтобы получить DNS-суффикс:

		ipconfig

	Запишите **DNS-суффикс, зависящий от подключения**. Например, *myhbasecluster.f5.internal.cloudapp.net*. При подключении к кластеру HBase необходимо подключиться к одному из Zookeeper, используя полное доменное имя. Каждый кластер HDInsight содержит 3 Zookeeper. Их имена — *zookeeper0*, *zookeeper1* и *zookeeper2*. Полное доменное имя будет примерно таким: *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Использование SQLLine**

1. Откройте **командную строку Hadoop** с рабочего стола.
2. Выполните следующие команды, чтобы открыть SQLLine:

		cd %phoenix_home%\bin
		sqlline.py [The FQDN of one of the Zookeepers]

	![hdinsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

	Команды, используемые в образце:

		CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
		
		!tables
		
		UPSERT INTO Company VALUES(1, 'Microsoft');
		
		SELECT * FROM Company;

Для получения дополнительных сведений обратитесь к разделу [Руководство по SQLLine](http://sqlline.sourceforge.net/#manual) и [Грамматика Phoenix](http://phoenix.apache.org/language/index.html).


















##Использование SQuirreL

[SQL-клиент SQuirreL ](http://squirrel-sql.sourceforge.net/) — это графическая программа Java, позволяющая просматривать структуру JDBC-совместимой базы данных, просматривать данные в таблицах, выполнять команды SQL и т. д. Используется для подключения к Phoenix Apache на HDInsight.

В этом разделе показано, как установить и настроить SQuirreL на рабочей станции для подключения к кластеру HBase в HDInsight через VPN.

###Предварительные требования

Перед выполнением процедур необходимо следующее:

- Кластер HBase, развернутый в виртуальной сети Azure с виртуальной машиной DNS. Инструкции см. в разделе [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]. 

	>[AZURE.IMPORTANT] Необходимо установить DNS-сервер в виртуальной сети. Для получения инструкций обратитесь к разделу [Настройка VPN-подключения между двумя виртуальными сетями Azure](hdinsight-hbase-geo-replication-configure-DNS.md).

- Получить для кластера HBase DNS-суффикс, зависящий от подключения. Для этого войдите в кластер с помощью RDP и запустите команду IPConfig. DNS-суффикс имеет следующий вид:

		myhbase.b7.internal.cloudapp.net
- Загрузить и установить [Microsoft Visual Studio Express 2013 для Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) на рабочую станцию. Необходимо выполнить команду makecert из пакета для создания сертификата.  
- Загрузить и установить среду [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) на рабочую станцию. Для SQL-клиента SQuirreL версии 3.0 и выше требуется JRE версии 1.6 или выше.  


###Настройка VPN-подключения «точка-сайт» к виртуальной сети Azure

Настройка VPN-подключения «точка-сайт» предусматривает 3 шага:

1. [Настройка виртуальной сети и шлюза динамической маршрутизации](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Создание сертификатов](#Create-your-certificates)
3. [Настройка VPN-клиента](#Configure-your-VPN-client)

Дополнительные сведения см. в разделе [Настройка VPN-подключения «точка-сайт» к виртуальной сети Azure](../vpn-gateway/vpn-gateway-point-to-site-create.md).

#### Настройка виртуальной сети и шлюза динамической маршрутизации

Обеспечьте наличие подготовленного кластера HBase в виртуальной сети Azure (см. предварительные условия для этого раздела). Следующий шаг — настроить подключение «точка-сайт».

**Настройка подключения «точка-сайт»**

1. Войдите на [классический портал Azure][azure-portal].
2. Слева щелкните **СЕТИ**.
3. Щелкните созданную вами виртуальную сеть(см. [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]).
4. Щелкните **НАСТРОИТЬ** в верхней части страницы.
5. В разделе **подключение «точка-сайт»** выберите **Настроить подключение «точка-сайт»**. 
6. Настройте **НАЧАЛЬНЫЙ IP-АДРЕС** и **CIDR** для указания диапазона IP-адресов, из которого клиенты VPN будут получать IP-адрес при подключении. Диапазон не может пересекаться с другими диапазонами, выделенными в локальной сети и виртуальной сети Azure, к которым будет выполняться подключение. Например, если выбран диапазон 10.0.0.0/20 для виртуальной сети, для пространства клиентских адресов можно выбрать 10.1.0.0/24. См. страницу [Подключение «точка-сайт»][vnet-point-to-site-connectivity] для получения дополнительной информации.
7. В разделе пространств адресов виртуальной сети щелкните **Добавить подсеть шлюза**.
7. В нижней части страницы нажмите **СОХРАНИТЬ**.
8. Нажмите кнопку **ДА** для подтверждения изменения. Подождите, пока система завершит внесение изменений, прежде чем перейти к следующей процедуре.


**Создание шлюза динамической маршрутизации**

1. На классическом портале Azure щелкните **ПАНЕЛЬ МОНИТОРИНГА** в верхней части страницы.
2. Щелкните **СОЗДАТЬ ШЛЮЗ** в нижней части страницы.
3. Нажмите кнопку **ДА** для подтверждения. Подождите, пока будет создан шлюз.
4. Щелкните **ПАНЕЛЬ МОНИТОРИНГА** в верхней части страницы. Появится визуальная схема виртуальной сети:

	![Схема виртуальной сети Azure «точка-сайт»][img-vnet-diagram]

	На схеме показано 0 клиентских подключений. После подключения к виртуальной сети число обновится до единицы.

#### Создание сертификатов

Одним из способов создания сертификата X.509 является использование средства создания сертификатов (makecert.exe), входящего в состав [Microsoft Visual Studio Express 2013 для Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).


**Создание самозаверяющего корневого сертификата**

1. На рабочей станции откройте окно командной строки.
2. Перейдите в папку средств Visual Studio. 
3. Следующая команда в приведенном ниже примере создаст и установит корневой сертификат в хранилище личных сертификатов на рабочей станции, а также создает соответствующий CER-файл, который позднее будет отправлен на классический портал Azure. 

		makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

	Перейдите в каталог, в котором должен располагаться CER-файл, где HBaseVnetVPNRootCertificate — имя, которое следует использовать для сертификата.

	Не закрывайте командную строку: Она понадобится в следующей процедуре.

	>[AZURE.NOTE] Поскольку был создан корневой сертификат, из которого будут создаваться клиентские сертификаты, вы можете экспортировать этот сертификат вместе с его закрытым ключом и сохранить в безопасном месте, где его можно будет восстановить.

**Создание клиентского сертификата**

- В той же командной строке (она должна быть открыта на том же компьютере, где был создан корневой сертификат. Клиентский сертификат необходимо создавать на основе корневого) выполните следующую команду:

  		makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

	HBaseVnetVPNRootCertificate — это имя корневого сертификата. Он должен соответствовать имени корневого сертификата.

	Корневой сертификат и сертификат клиента хранятся в хранилище личных сертификатов на компьютере. Используйте certmgr.msc для проверки.

	![Схема виртуальной сети Azure «точка-сайт»][img-certificate]

	Сертификат клиента необходимо установить на каждый компьютер, который будет подключаться к виртуальной сети. Рекомендуем создавать уникальные сертификаты клиентов для всех компьютеров, которые будут подключаться к виртуальной сети. Для экспорта сертификатов клиента используйте команду certmgr.msc.

**Передача корневого сертификата на классический портал Azure**

1. В левой части классического портала Azure щелкните **СЕТИ**.
2. Выберите виртуальную сеть, где развернут кластер HBase.
3. Вверху выберите **СЕРТИФИКАТЫ**.
4. Щелкните **ПЕРЕДАТЬ** снизу и укажите файл корневого сертификата, созданный двумя процедурами ранее. Подождите, пока сертификат будет импортирован.
5. В верхней части страницы щелкните **ПАНЕЛЬ МОНИТОРИНГА**. На виртуальной схеме будет показано состояние.


#### Настройка VPN-клиента



**Загрузка и установка пакета клиента VPN**

1. На странице «ПАНЕЛЬ МОНИТОРИНГА» виртуальной сети в области быстрого обзора **Загрузить 64-разрядный пакет клиента VPN** или **Загрузить 32-разрядный пакет клиента VPN** в зависимости от используемой версии ОС рабочей станции.
2. Щелкните **Выполнить** для установки пакета.
3. При появлении запроса системы безопасности нажмите **Сведения**, а затем **Выполнить в любом случае**.
4. Нажмите кнопку **Да** дважды.

**Подключение к VPN**

1. На рабочем столе рабочей станции щелкните значок «Сети» на панели задач. Должно отобразиться VPN-подключения с именем вашей виртуальной сети.
2. Щелкните имя VPN-подключения.
3. Щелкните **Подключить**.

**Проверка VPN-подключения и разрешения имени домена**

- На рабочей станции откройте командную строку и подайте команду ping по одному из следующих имен при условии, что DNS-суффикс кластера HBase — myhbase.b7.internal.cloudapp.net:

		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		zookeeper0.myhbase.b7.internal.cloudapp.net
		headnode0.myhbase.b7.internal.cloudapp.net
		headnode1.myhbase.b7.internal.cloudapp.net
		workernode0.myhbase.b7.internal.cloudapp.net

###Установка и настройка SQuirreL на рабочей станции

**Установка SQuirreL**

1. Загрузите jar-файл SQL-клиента SQuirreL с сайта [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Откройте/запустите jar-файл. Для него требуется среда [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Нажмите кнопку **Далее** дважды.
4. Укажите путь, для которого у вас есть разрешения на запись, и нажмите кнопку **Далее**.
	>[AZURE.NOTE] Папка установки по умолчанию — C:\\Program Files\\squirrel-sql-3.6. Для записи в эту папку программе установки необходимо предоставить права администратора. Можно открыть командную строку от имени администратора, перейти в папку bin Java, а затем выполнить
	>
	>     java.exe -jar [the path of the SQuirreL jar file] 
5. Нажмите **ОК** для подтверждения создания целевого каталога.
6. Настройка по умолчанию — установка базового и стандартного пакетов. Нажмите кнопку **Далее**.
7. Нажмите кнопку **Далее** дважды, а затем нажмите **Готово**.


**Установка драйвера Phoenix**

JAR-файл драйвера phoenix находится в кластере HBase. Путь подобен следующему в зависимости от версий:

	C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Этот файл необходимо скопировать на рабочую станцию по пути [папка установки SQuirrel]/lib. Самый простой способ — войти в кластер с помощью RDP, а затем с помощью команд копирования и вставки (CTRL+C и CTRL+V) скопировать файл на рабочую станцию.

**Добавление драйвера Phoenix в SQuirreL**

1. Откройте SQL-клиент SQuirreL на рабочей станции.
2. Выберите вкладку **Драйвер** в левой части окна.
2. В меню **Драйверы** выберите **Новый драйвер**.
3. Введите следующие сведения:

	- **Имя**: Phoenix
	- **Пример URL-адреса**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
	- **Имя класса**: org.apache.phoenix.jdbc.PhoenixDriver

	>[AZURE.WARNING] В примере URL-адреса следует использовать исключительно символы нижнего регистра. Можно использовать весь набор zookeeper в случае, если один из них не работает. Их имена — zookeeper0, zookeeper1 и zookeeper2.

	![Драйвер HDInsight HBase Phoenix SQuirreL][img-squirrel-driver]
4. Нажмите кнопку **ОК**.

**Создание псевдонима для кластера HBase**

1. В SQuirreL выберите вкладку **Псевдонимы** слева.
2. В меню **Псевдонимы** щелкните **Новый псевдоним**.
3. Введите следующие сведения:

	- **Имя**: имя кластера HBase или любое предпочитаемое имя.
	- **Драйвер**: Phoenix. Это значение должно соответствовать имени драйвера, созданному в предыдущей процедуре.
	- **URL-адрес**: копируется из конфигурации драйвера. Убедитесь, что используются исключительно символы нижнего регистра.
	- **Имя пользователя**: может быть любой текстовой строкой. Поскольку здесь используется VPN-подключение, имя пользователя не используется вообще.
	- **Пароль**: может быть любой текстовой строкой.

	![Драйвер HDInsight HBase Phoenix SQuirreL][img-squirrel-alias]
4. Нажмите **Проверить**. 
5. Щелкните **Подключить**. При установлении подключения SQuirreL выглядит следующим образом:

	![HBase Phoenix SQuirreL][img-squirrel]

**Выполнение проверки**

1. Выберите вкладку **SQL** непосредственно справа от вкладки **Объекты**.
2. Скопируйте следующий код и вставьте в Блокнот:

		CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Нажмите кнопку выполнения.

	![HBase Phoenix SQuirreL][img-squirrel-sql]
4. Переключитесь обратно на вкладку **Объекты**.
5. Разверните имя псевдонима, а затем разверните узел **ТАБЛИЦА**. В нем должна отобразиться новая таблица.
 
##Дальнейшие действия
В этой статье вы изучили использование Apache Phoenix в HDInsight. Дополнительные сведения см. на следующих ресурсах:

- [Обзор HDInsight HBase][hdinsight-hbase-overview]\: HBase — это NoSQL (нереляционная распределенная) база данных с открытым исходным кодом Apache, разработанная в рамках проекта Hadoop, которая обеспечивает произвольный доступ и строгую согласованность больших объемов неструктурированных и полуструктурированных данных.
- [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]\: благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения, поэтому эти приложения могут напрямую обмениваться данными с HBase.
- [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md): узнайте, как настроить репликацию HBase между двумя центрами обработки данных Azure. 
- [Анализ мнений Twitter с помощью HBase в HDInsight][hbase-twitter-sentiment]\: узнайте, как выполнять [анализ мнений](http://en.wikipedia.org/wiki/Sentiment_analysis) в режиме реального времени на основе больших данных, используя HBase в кластере Hadoop в HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 

<!---HONumber=AcomDC_0309_2016-->