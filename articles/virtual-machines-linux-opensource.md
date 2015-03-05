<properties
  pageTitle="Linux и вычисления с открытым кодом в Azure"
  description="Этот раздел содержит перечень тем по Linux и вычислениям с открытым кодом в Azure, включая базовые принципы использования Linux, некоторые основные понятия о запуске или отправке образов Linux в Azure, а также другие сведения о конкретных технологиях и оптимизациях."
  documentationCenter=""
  authors="squillace"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="02/17/2015"
  ms.author="rasquill"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Linux и вычисления с открытым кодом в Azure

В этом документе предпринята попытка собрать в одном месте список всех статей, написанные сотрудниками и партнерами корпорации Майкрософт о запуске виртуальных машин под управлением Linux, а также других открытых вычислительных сред и приложений на платформе Microsoft Azure. Поскольку как платформа Azure, так и мир вычислений с открытым кодом активно развиваются, можно с большой вероятностью утверждать, что этот документ уже устарел, хотя мы стараемся постоянно добавлять новые статьи и удалять неактуальные. Если мы что-то упустили, напишите об этом в комментариях или направьте запрос на получение в наш [репозиторий Github](https://github.com/Azure/azure-content/).

разделы упорядочены следующим образом. (Ссылки могут указываться в нескольких разделах, так как статьи могут охватывать несколько концепций, дистрибутивов или технологий.)

- [Дистрибутивы](#distros) &mdash; Статьи, относящиеся к определенному дистрибутиву.
- [Основы](#basics) &mdash; Множество основных концепций, которые вы уже знаете либо должны знать.
- [Образы и репозитории сообщества](#images) &mdash; Другие источники крайне полезной информации, репозитории и двоичные файлы.
- [Языки и платформы](#langsandplats)
- [Примеры и сценарии](#samples)
- [Проверка подлинности и шифрование](#security) &mdash; Статьи, которые важны с точки зрения безопасности, однако могут относиться к Azure лишь косвенно.
- [Разработка, управление и оптимизация](#devops) &mdash; Большая и быстро меняющаяся категория.
- [Поддержка, устранение неполадок и описание проблемных ситуаций](#supportdebug) &mdash; Понятно из названия.

Кроме того, существует несколько статей, описывающих различные параметры Linux, репозитории образов, примеры и инструкции по отправке пользовательских образов. 

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Мероприятия и демонстрации. Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Практическое руководство. Отправка собственного образа дистрибутива](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd/) (а также инструкции по использованию [рекомендованных дистрибутивов Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/))
- [Примечания. Общие требования к Linux для выполнения в Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-generic/)
- [Примечания. Общие сведения о Linux в Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-introduction/)


### <a id='distros'>Дистрибутивы</a>

Существует множество дистрибутивов Linux, которые обычно упорядочены по системам управления пакетами: Некоторые основаны на dpgk, например Debian и Ubuntu, другие - на rpm, например CentOS, SUSE и RedHat. Некоторые компании, являющиеся официальными партнерами корпорации Майкрософт, предоставляют образы дистрибутивов, которые включаются в список рекомендуемых. Другие предоставляются сообществом. Приведенным в этом разделе дистрибутивам посвящены официальные статьи, даже если они использовались только в примерах к другим технологиям.


#### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu - это очень популярный и рекомендуемый для Azure дистрибутив Linux на основе управления пакетами dkpg и apt-get.

1. [Практическое руководство. Отправка собственного образа Ubuntu](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-ubuntu/)
2. [Практическое руководство. Стек LAMP Ubuntu](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
2. [Образы. Стек LAPP](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [Практическое руководство. Кластеры MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
4. [Практическое руководство. Node.js и Cassandra](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. [Практическое руководство. IPython Notebook](http://azure.microsoft.com/documentation/articles/virtual-machines-python-ipython-notebook/)
6. [Углубленные знания. Запуск ASP.NET 5 в Linux с помощью контейнеров Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [Образы. Redis Server](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [Образы. Minecraft Server](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [Образы. Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [Образы. Mono as a Service](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)

#### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian - это важный дистрибутив для Linux и всего мира программного обеспечения с открытым кодом, основанный на управлении пакетами dpgk и apt-get. MSOpenTech VM Depot содержит несколько образом, которые можно использовать.

#### CentOS

Дистрибутив CentOS Linux - это стабильная, предсказуемая, управляемая и воспроизводимая платформа, созданная на основе исходного кода Red Hat Enterprise Linux (RHEL).

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Коллекция образов](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [Практическое руководство. Подготовка пользовательской виртуальной машины на основе CentOS для Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-centos/)
4. [Блог. Развертывание образа виртуальной машины CentOS из OpenLogic](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Практическое руководство. Установка Apache Qpid Proton-C для AMQP и служебной шины](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [Образы. Apache 2.2.15 на базе OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [Образы. Drupal 7.2, LAMP Server на базе OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

#### SUSE Enterprise Linux и OpenSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Практическое руководство. Установка и запуск MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
12. [Практическое руководство. Подготовка пользовательской виртуальной машины SLES и openSUSE](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/)  
13. [[Форум по SUSE] Практическое руководство. Перемещение на новый сервер исправления](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Образы. SUSE Linux Enterprise Server для SAP Cloud Appliance Library](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

#### CoreOS

CoreOS - это небольшой оптимизированный дистрибутив, который ориентирован исключительно на вычисления и допускает гибкую настройку управления.

10. [Коллекция образов](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [Практическое руководство. Использование CoreOS в Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
12. [Блог. TechEd Europe - клиент Windows Docker и контейнеры Linux](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
13. [Блог. Azure - все больше, быстрее и открытее](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
14. [Github. Краткое руководство по развертыванию CoreOS в Azure](https://github.com/timfpark/coreos-azure)
15. [Github. Развертывание приложения Java с использованием Spring Boot, MongoDB и CoreOS](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Подготовка виртуальной машины Oracle Linux для Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-oracle/)

#### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Блог. Запуск FreeBSD в Azure](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Блог. Простое развертывание FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Блог. Развертывание пользовательского образа FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Практическое руководство. Установка агента Linux для Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
18. [Marketplace. Kaspersky AV для файлового сервера Linux](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a id='basics'>Основы</a>

1. [Основы. Интерфейс командной строки Azure (cli)](http://azure.microsoft.com/documentation/articles/xplat-cli/)
4. [Основы. Использование сертификатов и управление ими](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [Основы. Выбор имен пользователей Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-usernames/)
6. [Основы. Вход в виртуальную машину Linux через портал Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-log-on/)
7. [Основы. SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Основы. Сброс пароля или свойств SSH для Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Основы. Использование прав root](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)
10. [Основы. Подключение диска данных к виртуальной машине Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)
11. [Основы. Отключение диска данных от виртуальной машины Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-detach-disk/)
12. [Блоги, посвященные основам работы. Оптимизация хранилища, дисков и производительности с помощью Linux и Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Основы. RAID](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/)
14. [Основы. Запись виртуальной машины Linux для создания шаблона](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/)
15. [Основы. Агент Linux для Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
16. [Основы. Расширения и компоненты виртуальных машин Azur](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [Основы. Включение пользовательских данных в виртуальную машину Azure для использования с Cloud-init](http://azure.microsoft.com/documentation/articles/virtual-machines-how-to-inject-custom-data/)
18. [Блоги, посвященные основам работы. Построение высокодоступной системы Linux в Azure за 12 шагов](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Блоги, посвященные основам работы. Автоматизация подготовки Linux в Azure с помощью xplat, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
20. [Основы. Расширение виртуальных машин Docker для Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
23. [Справочник по REST API управления службами Azure](https://msdn.microsoft.com/ru-ru/library/azure/ee460799.aspx)

### <a id='images'>Образы и репозитории сообщества</a>
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; для предоставляемых сообществом образов виртуальных машин.
4. [Github](https://github.com/Azure/) &mdash; для xplat-cli и множества других средств и проектов.
5. [Docker Hub Registry](https://registry.hub.docker.com/) &mdash; реестр для образов контейнеров Docker.


### <a id='langsandplats'>Языки и платформы</a>
#### [Центр разработки на Java для Azure](http://azure.microsoft.com/develop/java/)

1. [Образы](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Практическое руководство. Использование служебной шины для Java с протоколом AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Практическое руководство. Настройка Tomcat7 в Linux с помощью портала Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-setup-tomcat7-linux/)
4. [Видео. Пакет Azure Java SDK для управления службами](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Блог. Приступая к работе с библиотеками управления Azure для Java](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [Репозиторий GitHub. Набор средств Azure для Eclipse с Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Справочник. Набор средств Azure для Eclipse с Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [Репозиторий GitHub. Подключаемый модуль MS Open Tech Tools для IntelliJ IDEA и Android Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Блог. Вклад MSOpenTech в OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Образы. WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Образы. WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [Образы. JDK6 на базе Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [Образы. JDK7 на базе Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [Образы. JDK8 на базе Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

#### Языки виртуальной машины Java

1. [Scala. Запуск приложений Play Framework в облачных службах Azure](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

#### Типы, виды установки и обновления пакетов SDK
4. [Пакет SDK для управления службами Azure. Java](http://dl.windowsazure.com/javadoc/)
5. [Пакет SDK для управления службами Azure. Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Пакет SDK для управления службами Azure. Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
- [Практическое руководство. Установка Ruby on Rails](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-rails-web-app-linux/)
- [Практическое руководство. Установка Ruby on Rails с использованием Capistrano, Nginx, Unicorn и PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)
6. [Пакет SDK для управления службами Azure. Python](https://github.com/Azure/azure-sdk-for-python)
- [Практическое руководство. Веб-приложение Hello World на Django (Mac-Linux)](http://azure.microsoft.com/documentation/articles/virtual-machines-python-django-web-app-linux/)
7. [Пакет SDK для управления службами Azure. Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Пакет SDK для управления службами Azure. PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
- [Практическое руководство. Установка стека LAMP на виртуальной машине Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
- [Видео. Установка стека LAMP на виртуальной машине Azure](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Пакет SDK для управления службами Azure. .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Блог. Mono, ASP.NET 5, Linux и Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### <a id='samples'>Примеры и сценарии</a>

1. [Репозиторий Github Azure Linux Патрика Чейзона (Patrick Chanezon)](https://github.com/chanezon/azure-linux)
3. [Видео. Перемещение локальных данных USB из Linux в Azure с помощью **usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Видео. Доступ к графическому интерфейсу на базе Linux в Azure в браузере с помощью fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Видео. Общее хранилище на базе Linux с использованием предварительной версии Azure Files - часть 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Видео. Использование устройств Linux в Azure с помощью служебной шины и веб-сайтов](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Видео. Подключение собственного кэшированного в памяти приложения на основе Linux к Windows Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Видео. Балансировка нагрузки для служб высокой доступности Linux в Azure. OpenLDAP и MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


### <a id='data'>Данные</a>

Этот раздел содержит сведения о нескольких разных подходах к хранению и соответствующих технологиях, включая NoSQL, реляционные данные и большие объемы данных.

#### Nosql

1. [Блог. 8 баз данных NoSql с открытым исходным кодом для Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
- [Slideshare (MSOpenTech). Опыте работы с CouchDb в Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Блог. Запуск CouchDB как службы с помощью node.js, CORS и Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
- [Практическое руководство. Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab](http://azure.microsoft.com/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/)
4. Cassandra
- [Практическое руководство. Запуск Cassandra под управлением Linux в Azure и доступ к нему из Node.js](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. Redis
- [Блог. Redis на базе Windows в службе кэша Azure Redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Блог. Анонс поставщика состояний сеансов ASP.NET для предварительной версии Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
- [Блог. RavenHQ теперь доступен в Azure Marketplace](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

#### Данные большого размера
2. Hadoop/Cloudera  
	- [Блог. Установка Hadoop на виртуальных машинах Linux в Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [Практическое руководство. Приступая к работе с Hadoop и Hive с помощью HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-get-started/)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) - полностью управляемая служба Hadoop в Azure.

#### Реляционные данные
2. MySQL
- [Практическое руководство. Установка и запуск MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
- [Практическое руководство. Оптимизация производительности MySQL в Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/)
- [Практическое руководство. Кластеры MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
- [Практическое руководство. Создание базы данных MySQL в Marketplace](http://azure.microsoft.com/documentation/articles/store-php-create-mysql-database/)
- [Практическое руководство. Django и MySQL на базе веб-сайтов Azure с использованием Python и Visual Studio](http://azure.microsoft.com/documentation/articles/web-sites-python-ptvs-django-mysql/)
- [Практическое руководство. PHP и MySQL на базе веб-сайтов Azure с использованием WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/)
7. MariaDB
- [Практическое руководство. Создание кластера MariaDbs с несколькими источниками](http://azure.microsoft.com/documentation/articles/virtual-machines-mariadb-cluster/)
7. PostgreSQL
- [Практическое руководство. Установка Ruby on Rails с использованием Capistrano, Nginx, Unicorn и PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)


### <a id='security'>Проверка подлинности и шифрование</a>

Проверка подлинности и шифрование играют важную роль в разработке программного обеспечения, и в Интернете существует множество статей, помогающих освоить и использовать соответствующие методики обеспечения безопасности. Мы приводим несколько основных принципов использования, позволяющих быстро запустить Linux и начать обработку рабочих нагрузок с открытым кодом, а также упоминаем средства для сброса или удаления компонентов удаленной безопасности в Azure.

4. [Основы. Использование сертификатов и управление ими](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Основы. SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Основы. Сброс пароля или свойств SSH для Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Основы. Использование прав root](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)

### <a id='devops'>Разработка, управление и оптимизация</a>

Этот раздел начинается с записи блога, содержащей несколько видеороликов на странице [Видео. Виртуальные машины Azure: использование Chef, Puppet и Docker для управления виртуальными машинами Linux](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Однако мир разработки, управления и оптимизации очень быстро изменяется, поэтому используйте следующий перечень лишь в качестве отправной точки.

1. Docker
	- [Расширения виртуальных машин Docker для Linux в Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
	- [Использование расширения виртуальных машин Docker в кроссплатформенном интерфейсе командной строки Azure (xplat-cli)](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/)
	- [Использование расширения виртуальных машин Docker на портале Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/)
	- [Быстрое начало работы с Docker в Azure Marketplace](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/)
2. [Использование Fleet в CoreOS](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
3. Deis
	- [Репозиторий GitHub.
Установка Deis в кластере CoreOS в Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins and Hudson
	- [Блог. Подключаемый модуль ведомого режима Jenkins для Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Репозиторий GitHub. Подключаемый модуль хранилища Jenkins для Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Сторонние решения. Подключаемый модуль ведомого режима Hudson для Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Сторонние решения. Подключаемый модуль хранилища Hudson для Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef и виртуальные машины](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-install-chef-client/)
	- [Видео. Что такое Chef и как это работает?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Служба автоматизации Azure
	- [Видео. Использование службы автоматизации Azure для виртуальных машин Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Powershell DSC для Linux
    - [Блог. Как сделать Powershell DSC для Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [Github. DSC клиента Docker](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)

### <a id='supportdebug'>Поддержка, устранение неполадок и описание проблемных ситуаций</a>

1. Посвященная поддержке документация корпорации Майкрософт
	- [Поддержка. Поддержка образов Linux в Microsoft Azure](http://support2.microsoft.com/kb/2941892)


<!--Anchors-->
[Дистрибутивы]: #distros
[Основы]: #basics
[Образы и репозитории сообщества]: #images
[Языки и платформы]: #langsandplats
[Примеры и сценарии]: #samples
[Проверка подлинности и шифрование]: #security
[Разработка, управление и оптимизация]: #devops
[Поддержка, устранение неполадок и описание проблемных ситуаций]: #supportdebug

<!--HONumber=45--> 
