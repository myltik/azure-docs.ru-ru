Выполните следующие действия, чтобы установить и запустить MongoDB на виртуальной машине CentOS Linux.

> [AZURE.WARNING]Функции безопасности MongoDB, такие как проверка подлинности и привязка IP-адреса, не включены по умолчанию. Функции безопасности необходимо включить перед развертыванием MongoDB в рабочей среде. Дополнительные сведения см. в документе [Безопасность и проверка подлинности](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Настройте систему управления пакетами (YUM), чтобы можно было установить MongoDB. Создайте файл */etc/yum.repos.d/10gen.repo* для хранения информации о репозитории и добавьте в него следующее:

		[10gen]
		name=10gen Repository
		baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
		gpgcheck=0
		enabled=1

2. Сохраните REPO-файл и выполните следующую команду, чтобы обновить локальную базу данных пакетов:

		$ sudo yum update

3. Чтобы установить последнюю стабильную версию MongoDB и связанные с ней средства, выполните следующую команду:

		$ sudo yum install mongo-10gen mongo-10gen-server

	Подождите, пока MongoDB загрузится и установится.

4. Создайте каталог данных. По умолчанию MongoDB хранит данные в каталоге */data/db*, но этот каталог необходимо создать. Чтобы создать каталог, выполните следующую команду:

		$ sudo mkdir -p /srv/datadrive/data
		$ sudo chown `id -u` /srv/datadrive/data

	Дополнительные сведения об установке MongoDB на Linux см. в документе [Quickstart Unix][QuickstartUnix].

5. Чтобы запустить базу данных, введите:

		$ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

	Все сообщения журнала будут направляться в файл */srv/datadrive/data/mongod.log*, когда сервер MongoDB запустится и выделит место для файлов журналов. Может пройти несколько минут, пока MongoDB выделит место для файлов журналов и начнет прослушивать подключения.

6. Чтобы запустить консоль управления MongoDB, откройте отдельное окно SSH или PuTTY и введите:

		$ mongo
		> db.foo.save ( { a:1 } )
		> db.foo.find()
		{ _id : ..., a : 1 }
		> show dbs  
		...
		> show collections  
		...  
		> help  

	База данных создается путем вставки.

7. После установки MongoDB необходимо настроить конечную точку для удаленного доступа к MongoDB. На портале управления щелкните **Виртуальные машины** и выберите имя новой виртуальной машины, а затем щелкните **Конечные точки**.
	
	![Конечные точки][Image7]

8. Щелкните **Добавить конечную точку** в нижней части страницы.
	
	![Конечные точки][Image8]

9. Добавьте конечную точку со следующими параметрами:

 - **Имя**: Mongo
 - **Протокол**: TCP
 - **Общий порт**: 27017
 - **Закрытый порт**: 27017
 
 Это позволит получать удаленный доступ к MongoDB.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png

<!---HONumber=July15_HO4-->