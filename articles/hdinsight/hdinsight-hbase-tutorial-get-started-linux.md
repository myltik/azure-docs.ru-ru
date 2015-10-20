<properties
	pageTitle="Учебник по HBase: Приступая к работе с HBase на Hadoop | Microsoft Azure"
	description="Следуйте инструкциям этого учебника по HBase, чтобы начать работу с Apache HBase на Hadoop в HDInsight. Создание таблиц из оболочки HBase и обращение к ним с помощью Hive."
	keywords="apache hbase,hbase,hbase shell,hbase tutorial"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/18/2015"
	ms.author="jgao"/>



# Учебник по HBase: Приступая к работе с Apache HBase на Hadoop в HDInsight

Узнайте, как настраивать кластер HBase на HDInsight, создавать таблицы HBase и запрашивать таблицы с помощью Hive. Для получения общих сведений о HBase обратитесь к разделу [Обзор HDInsight HBase][hdinsight-hbase-overview].

> [AZURE.NOTE]Информация, приведенная в этом документе, относится только к кластерам HDInsight на платформе Linux. Сведения о кластерах под управлением Windows см. в статье [Приступая к работе с Apache HBase на Hadoop в HDInsight (Windows).

##Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- PuTTY и PuTTYGen для клиентов Windows. Эти служебные программы доступны по адресу [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- [curl](http://curl.haxx.se/download.html).

## Подготовка кластера HBase

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Подготовка кластера HBase с помощью портала предварительной версии Azure**


1. Выполните вход на [портал предварительной версии Azure][azure-portal].
2. Щелкните **Создать** в верхнем левом углу и выберите **Данные + аналитика**, **HDInsight**.
3. Введите следующие значения.

	- **Имя кластера** — введите имя для идентификации этого кластера.
	- **Тип кластера** — выберите **HBase**.
	- **Кластер операционной системы** — выберите **Ubuntu**.
	- **Подписка** — выберите подписку Azure, используемую для подготовки этого кластера.
	- **Группа ресурсов** — добавьте группу ресурсов Azure или выберите существующую. Дополнительные сведения см. в статье [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md).
	- **Учетные данные**. При появлении запроса введите пароль для пользователя веб-службы HTTP. Имя пользователя по умолчанию — **admin**. Необходимо также ввести **Имя пользователя SSH** и **ПАРОЛЬ** или **ОТКРЫТЫЙ КЛЮЧ**, которые будут использоваться для проверки подлинности пользователя SSH. Мы рекомендуем использовать открытый ключ. Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях.

		- [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
		- [Использование SSH в Hadoop на HDInsight под управлением Linux из Windows](hdinsight-hadoop-linux-use-ssh-windows.md) Нажмите кнопку **Выбрать**, чтобы сохранить изменения.
	- **Data Source** — укажите существующую или создайте новую учетную запись хранения Azure, которая используется в качестве файловой системы по умолчанию для кластера. Расположение учетной записи хранения определяет расположение кластера. Кластер HDInsight и зависимая учетная запись хранения Azure должны находиться в одном и том же центре обработки данных. По умолчанию в качестве имени **контейнера по умолчанию** используется имя кластера.  
	- **Укажите ценовые категории** — выберите количество серверов в регионе для кластера HBase.

		> [AZURE.WARNING]Для обеспечения высокой доступности служб HBase необходимо подготовить кластер, содержащий по крайней мере **три** узла. Это гарантирует, что если один узел выйдет из строя, области данных HBase будут доступны на других узлах.

	- **Необязательная конфигурация** — выберите версию кластера, настройте виртуальную сеть Azure, метахранилище Hive или Oozie, действия сценария и добавьте учетные записи хранения.

4. Щелкните **Создать**.

>[AZURE.NOTE]После удаления кластера HBase можно создать другой кластер HBase, использовав тот же контейнер BLOB-объектов по умолчанию. Таблицы HBase, созданные в исходном кластере, получит новый кластер.

## Использование оболочки HBase

Для большинства пользователей данные отображаются в табличном формате:

![табличные данные hdinsight hbase][img-hbase-sample-data-tabular]

В HBase, который является реализацией BigTable, те же данные выглядят следующим образом:

![данные bigtable hdinsight hbase][img-hbase-sample-data-bigtable]

Это будет нести больше смысла после завершения следующей процедуры.


**Использование оболочки HBase**

>[AZURE.NOTE]Приведенные здесь инструкции относятся к компьютерам под управлением Windows. Инструкции по подключению к кластеру HDInsight под управлением Linux из Linux, Unix или OS X см. в статье [Использование SSH с Hadoop под управлением Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md) 1. Откройте **PuTTY**. См. раздел «Предварительные требования» в начале этой статьи. 2. Если при создании учетной записи пользователя в процессе подготовки вы указали ключ SSH, выполните следующий шаг, чтобы выбрать закрытый ключ, который будет использоваться при аутентификации в кластере.

	In **Category**, expand **Connection**, expand **SSH**, and select **Auth**. Finally, click **Browse** and select the .ppk file that contains your private key.

3. В разделе **Категория** щелкните **Сеанс**.
4. На экране «Основные параметры вашего сеанса PuTTY» введите следующие значения:

	- Имя узла (или IP-адрес) — введите адрес SSH своего сервера HDInsight в поле «Имя узла» (или «IP-адрес»). Адрес SSH — это имя кластера с суффиксом **-ssh.azurehdinsight.net**. Например, *mycluster-ssh.azurehdinsight.net*.
	- Порт: 22. Порт SSH в headnode0 — 22. См. статью [Сведения об использовании HDInsight в Linux (предварительная версия)](hdinsight-hadoop-linux-information.md#remote-access-to-services).
4. Щелкните **Открыть** для подключения к кластеру.
5. При появлении запроса введите имя пользователя, которое было введено при создании кластера. Если для пользователя использовался пароль, отобразится также запрос на ввода пароля.
6. Выполните следующую команду:

		hbase shell

4. Создайте HBase с двумя столбцами:

		create 'Contacts', 'Personal', 'Office'
		list
5. Вставьте какие-либо данные:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![оболочка hbase hdinsight hadoop][img-hbase-shell]

6. Получите одну строку

		get 'Contacts', '1000'

	Вы увидите те же результаты, как при использовании команды сканирования, так как в таблице есть только одна строка.

	Дополнительную информацию о схеме таблицы Hbase см. в разделе [Введение в проектирование схемы HBase][hbase-schema]. Дополнительные команды HBase см. в [справочнике по Apache HBase][hbase-quick-start].


6. Выйдите из оболочки

		exit

**Для массовой загрузки данных в таблицу контактов HBase**

HBase включает несколько методов загрузки данных в таблицы. Для получения дополнительных сведений обратитесь к разделу [Массовая загрузка](http://hbase.apache.org/book.html#arch.bulk.load).


Образец файла данных был загружен в контейнер открытого BLOB-объекта wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. Файл данных содержит:

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

Можно создать текстовый файл и отправить его на собственную учетную запись хранения, если потребуется. Указания см. в разделе [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data].

> [AZURE.NOTE]Эта процедура использует таблицу контактов HBase, созданную в предыдущей процедуре.

1. Откройте **PuTTY** и подключитесь к кластеру. См. инструкции в приведенной выше процедуре.
3. Выполните следующую команду, чтобы преобразовать файл данных в StoreFiles и сохранить по относительному пути, указанному в Dimporttsv.bulk.output:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Выполните следующую команду для передачи данных из /example/data/storeDataFileOutput в таблицу HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Откройте оболочку HBase и выполните команду сканирования для получения списка содержимого таблицы.



## Использование HiveQL для формирования запросов к таблицам HBase

Можно запросить данные в таблицах HBase с помощью Hive. В данном разделе будет создана таблица Hive, которая сопоставляется с существующей таблицей HBase и используется для формирования запросов данных таблицы HBase.


1. Откройте **PuTTY** и подключитесь к кластеру. См. инструкции в приведенной выше процедуре.
2. Откройте оболочку Hive.

	hive
3. Выполните приведенный ниже сценарий HiveQL, чтобы создать таблицу Hive, сопоставляемую с таблицей HBase. Перед выполнением этого оператора убедитесь, что вы создали упомянутый в этом учебнике пример таблицы с помощью оболочки HBase.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Запустите следующий сценарий HiveQL. Запрос Hive запрашивает данные в таблице HBase:

     	SELECT count(*) FROM hbasecontacts;

## Использование API REST для HBase

> [AZURE.NOTE]При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
>
> В командах, описанных в этом разделе, замените **USERNAME** на имя пользователя для выполнения проверки подлинности в кластере, а **PASSWORD** — на пароль учетной записи пользователя. Параметр **CLUSTERNAME** требуется заменить именем кластера.
>
> API REST защищается с помощью [обычной проверки подлинности](http://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Используйте следующую команду в командной строке, чтобы проверить возможность подключения к кластеру HDInsight:

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	Вы должны получить ответ, аналогичный показанному ниже:

    {"status":"ok","version":"v1"}

  Ниже приведены параметры, используемые в этой команде:

    * **-u** - The user name and password used to authenticate the request.
    * **-G** - Indicates that this is a GET request.

2. Для получения списка существующих таблиц HBase используйте следующую команду:

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Для создания новой таблицы HBase с двумя семействами столбцов используйте следующую команду:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"@name":"test","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}"

	Схема предоставляется в формате JSON.

4. Чтобы вставить какие-либо данные, используйте следующую команду:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"Row":{"key":"1000","Cell":{"column":"Personal:Name", "$":"John Dole"}}}"

5. Для получения строки используйте следующую команду:

		curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## Проверка состояния кластера

HBase на HDInsight поставляется с веб-интерфейсом для наблюдения за кластерами. С помощью веб-интерфейса вы можете запросить статистику или сведения о регионах.

SSH может также использоваться для туннелирования локальных запросов, например веб-запросов, к кластеру HDInsight. Запрос будет затем перенаправлен к запрошенному ресурсу, как если бы исходил от головного узла кластера HDInsight. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Windows (предварительная версия)](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Создание сеанса туннелирования SSH**

1. Откройте **PuTTY**.  
2. Если при создании учетной записи пользователя в процессе подготовки вы указали ключ SSH, выполните следующий шаг, чтобы выбрать закрытый ключ, который будет использоваться при аутентификации в кластере.

	В разделе **Категория** последовательно разверните **Подключение**, **SSH** и выберите **Аутентификация**. Наконец, нажмите кнопку **Обзор** и выберите PPK-файл, содержащий закрытый ключ.

3. В разделе **Категория** щелкните **Сеанс**.
4. На экране «Основные параметры вашего сеанса PuTTY» введите следующие значения:

	- **Имя узла** — введите адрес SSH своего сервера HDInsight в поле «Имя узла» (или «IP-адрес»). Адрес SSH — это имя кластера с суффиксом **-ssh.azurehdinsight.net**. Например, *mycluster-ssh.azurehdinsight.net*.
	- **Порт**: 22. Порт SSH в headnode0 — 22.  
5. В разделе **Категория** в левой части диалогового окна последовательно разверните узлы **Подключение** и **SSH** и выберите **Туннели**.
6. Введите следующую информацию в форме «Параметры, управляющие перенаправлением портов SSH»:

	- **Порт источника** — порт на стороне клиента, трафик которого нужно перенаправлять. Например, 9876.
	- **Динамическая** — включает динамическую маршрутизацию прокси-сервера SOCKS.
7. Щелкните **Добавить**, чтобы добавить параметры.
8. Щелкните **Открыть** в нижней части диалогового окна, чтобы открыть подключение SSH.
9. При появлении запроса войдите на сервер, используя учетную запись SSH. При этом будет установлен сеанс SSH и включен туннель.

**Поиск FQDN для Zookeeper с помощью Ambari**

1. Перейдите по адресу https://<ClusterName>.azurehdinsight.net/.
2. Дважды введите данные учетной записи кластера пользователя.
3. В меню слева выберите пункт **Zookeeper**.
4. Щелкните одну из трех ссылок **Сервер Zookeeper** в общем списке.
5. Скопируйте **имя узла**. Например, zookeepernode0.zookeepernode-0-xxxxxxxxxxxxxxxxxxxx.c7.internal.cloudapp.net.

**Настройка клиента (Firefox) и проверка состояния кластера**

1. Откройте Firefox.
2. Нажмите кнопку **Открыть меню**.
3. Щелкните по элементу **Параметры**.
4. Последовательно выберите пункты **Дополнительно** > **Сеть** > **Параметры**.
5. Выберите параметр **Настройка прокси-сервера вручную**.
6. Введите следующие значения.

	- **Хост Socks**: localhost
	- **Порт** — порт, указанный для туннелирования SSH с использованием PuTTY. Например, 9876.
	- **SOCKS v5**: (выбрано)
	- **Удаленный DNS**: (выбрано)
7. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.
8. Перейдите по адресу http://<TheFQDN of a ZooKeeper>:60010/master-status

В высокодоступном кластере вы найдете ссылку на текущий активный главный узел HBase, где размещается веб-интерфейс.



## Что дальше?
В этом учебном материале по HBase для HDInsight вы узнали, как подготовить кластер HBase, а также как создавать таблицы и просматривать данные в этих таблицах из оболочки HBase. Вы также узнали, как использовать Hive для запроса данных из таблиц HBase и как использовать интерфейсы REST API на C# для HBase для создания таблицы HBase и извлечения данных из таблицы.

Дополнительные сведения см. на следующих ресурсах:

- [Обзор HDInsight HBase][hdinsight-hbase-overview]\: HBase — это NoSQL (нереляционная распределенная) база данных с открытым исходным кодом Apache, разработанная в рамках проекта Hadoop, которая обеспечивает произвольный доступ и строгую согласованность больших объемов неструктурированных и полуструктурированных данных.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=Oct15_HO3-->