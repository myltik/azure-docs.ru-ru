<properties
	pageTitle="Linux и вычисления с открытым кодом в Azure"
	description="Этот раздел содержит перечень тем по Linux и вычислениям с открытым кодом в Azure, включая базовые принципы использования Linux, некоторые основные понятия о запуске или отправке образов Linux в Azure, а также другие сведения о конкретных технологиях и оптимизациях."
	services="virtual-machines"
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
	ms.date="09/02/2015"
	ms.author="rasquill"/>



# Linux и вычисления с открытым кодом в Azure

В этом документе предпринята попытка собрать в одном месте список всех разделов, написанных сотрудниками и партнерами корпорации Майкрософт о запуске виртуальных машин под управлением Linux, а также других открытых вычислительных сред и приложений на платформе Microsoft Azure. Так как и платформа Azure, и мир вычислений с открытым исходным кодом активно развиваются, можно с большой вероятностью утверждать, что этот документ уже устарел, *хотя* мы стараемся постоянно добавлять новые разделы и удалять неактуальные. Если мы что-то упустили, напишите об этом в комментариях или направьте запрос на получение в наш [репозиторий Github](https://github.com/Azure/azure-content/).

## Общие примечания
Разделы расположены в правой части этой страницы. (Ссылки могут указываться в нескольких подразделах, так как разделы могут охватывать несколько концепций, дистрибутивов или технологий.) Кроме того, существует несколько разделов, описывающих различные параметры Linux и репозитории образов, которые содержат примеры и инструкции по отправке пользовательских образов.

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Мероприятия и демонстрации. Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Практическое руководство. Передача собственного образа дистрибутива](virtual-machines-linux-create-upload-vhd.md) (а также инструкции по использованию [рекомендованных дистрибутивов Azure](virtual-machines-linux-endorsed-distributions.md))
- [Примечания. Общие требования к Linux для выполнения в Azure](virtual-machines-linux-create-upload-vhd-generic.md)
- [Примечания. Введение в Linux в Azure](virtual-machines-linux-introduction.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## Дистрибутивы

Существует множество дистрибутивов Linux, которые обычно упорядочены по системам управления пакетами: некоторые основаны на dpgk, например Debian и Ubuntu, другие — на rpm, например CentOS, SUSE и RedHat. Некоторые компании, являющиеся официальными партнерами корпорации Майкрософт, предоставляют образы дистрибутивов, которые включаются в список рекомендуемых. Другие предоставляются сообществом. Приведенным в этом разделе дистрибутивам посвящены официальные статьи, даже если они использовались только в примерах к другим технологиям.

### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu — это очень популярный и рекомендуемый для Azure дистрибутив Linux на основе управления пакетами dkpg и apt-get.

1. [Практическое руководство. Передача собственного образа Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)
2. [Практическое руководство. Стек LAMP Ubuntu](virtual-machines-linux-install-lamp-stack.md)
2. [Образы. Стек LAPP](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [Практическое руководство. Кластеры MySQL](virtual-machines-linux-mysql-cluster.md)
4. [Практическое руководство. Node.js и Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)
5. [Практическое руководство. IPython Notebook](virtual-machines-python-ipython-notebook.md)
6. [Углубленные знания. Запуск ASP.NET 5 в Linux с помощью контейнеров Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [Образы. Redis Server](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [Образы. Minecraft Server](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [Образы. Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [Образы. Mono as a Service](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian — это важный дистрибутив для Linux и всего мира программного обеспечения с открытым исходным кодом, основанный на управлении пакетами dpgk и apt-get. MSOpenTech VM Depot содержит несколько образом, которые можно использовать.

### CentOS

Дистрибутив CentOS Linux — это стабильная, предсказуемая, управляемая и воспроизводимая платформа, созданная на основе исходного кода Red Hat Enterprise Linux (RHEL).

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Коллекция образов](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [Практическое руководство. Подготовка пользовательской виртуальной машины на основе CentOS для Azure](virtual-machines-linux-create-upload-vhd-centos.md)
4. [Блог. Развертывание образа виртуальной машины CentOS из OpenLogic](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Практическое руководство. Установка Apache Qpid Proton-C для AMQP и служебной шины](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [Образы. Apache 2.2.15 на базе OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [Образы. Drupal 7.2, LAMP Server на базе OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

### SUSE Linux Enterprise Server и openSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Практическое руководство. Установка и запуск MySQL](virtual-machines-linux-mysql-use-opensuse.md)
12. [Практическое руководство. Подготовка пользовательской виртуальной машины SLES или openSUSE](virtual-machines-linux-create-upload-vhd-suse.md)  
13. [[Форум по SUSE] Практическое руководство. Переход на новый сервер исправлений](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Образы. SUSE Linux Enterprise Server для SAP Cloud Appliance Library](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS — это небольшой оптимизированный дистрибутив, который ориентирован исключительно на вычисления и допускает гибкую настройку управления.

10. [Коллекция образов](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [Практическое руководство. Использование CoreOS в Azure](virtual-machines-linux-coreos-how-to.md)
12. [Практическое руководство. Начало работы с Fleet и Docker на CoreOS в Azure](virtual-machines-linux-coreos-fleet-get-started.md)
13. [Блог. TechEd Europe — клиент Windows Docker и контейнеры Linux](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
14. [Блог. Azure — все больше, быстрее и открытее](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
15. [GitHub. Краткое руководство по развертыванию CoreOS в Azure](https://github.com/timfpark/coreos-azure)
16. [GitHub. Развертывание приложения Java с использованием Spring Boot, MongoDB и CoreOS](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Подготовка виртуальной машины Oracle Linux для Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Блог. Запуск FreeBSD в Azure](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Блог. Простое развертывание FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Блог. Развертывание пользовательского образа FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Практическое руководство. Установка агента Linux для Azure](virtual-machines-linux-agent-user-guide.md)
18. [Marketplace. Kaspersky AV для файлового сервера Linux](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Основные сведения

1. [Основные сведения. Интерфейс командной строки Azure (CLI Azure)](../xplat-cli-install.md)
4. [Основные сведения. Использование сертификатов и управление ими](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [Основные сведения. Выбор имен пользователей Linux](virtual-machines-linux-usernames.md)
6. [Основные сведения. Вход на виртуальную машину Linux через портал Azure](virtual-machines-linux-how-to-log-on.md)
7. [Основные сведения. SSH](virtual-machines-linux-use-ssh-key.md)
8. [Основные сведения. Сброс пароля или свойств SSH для Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [Основные сведения. Использование прав корневой учетной записи](virtual-machines-linux-use-root-privileges.md)
10. [Основные сведения. Подключение диска данных к виртуальной машине Linux](virtual-machines-linux-how-to-attach-disk.md)
11. [Основные сведения. Отключение диска данных от виртуальной машины Linux](virtual-machines-linux-how-to-detach-disk.md)
12. [Блог основных сведений. Оптимизация хранилища, дисков и производительности с помощью Linux и Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Основные сведения. RAID](virtual-machines-linux-configure-raid.md)
14. [Основные сведения. Запись виртуальной машины Linux для создания шаблона](virtual-machines-linux-capture-image.md)
15. [Основные сведения. Агент Linux для Azure](virtual-machines-linux-agent-user-guide.md)
16. [Основные сведения. Расширения и компоненты виртуальных машин Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [Основные сведения. Включение пользовательских данных в виртуальную машину для использования с пакетом Cloud-init](virtual-machines-how-to-inject-custom-data.md)
18. [Блог основных сведений. Сборка высокодоступной системы Linux в Azure за 12 шагов](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Блог основных сведений. Автоматизация подготовки Linux в Azure с помощью CLI Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
19. [Развертывание нескольких виртуальных машин с помощью интерфейса командной строки Azure](virtual-machines-create-multi-vm-deployment-../xplat-cli-install.md)
20. [Основные сведения. Расширение виртуальной машины Docker для Azure](virtual-machines-docker-vm-extension.md)
23. [Справочник по API-интерфейсу управления службами REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)
24. [Файловая система Gluster в Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Образы и репозитории сообщества
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) — для предоставляемых сообществом образов виртуальных машин.
4. [GitHub](https://github.com/Azure/) — для CLI Azure и множества других средств и проектов.
5. [Docker Hub Registry](https://registry.hub.docker.com/) — реестр для образов контейнеров Docker.

## Языки и платформы
### [Центр разработки на Java для Azure](http://azure.microsoft.com/develop/java/)

1. [Образы](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Практическое руководство. Использование служебной шины из Java с помощью AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Практическое руководство. Настройка Tomcat7 в Linux с использованием портала Azure](virtual-machines-linux-setup-tomcat7-linux.md)
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

### Языки виртуальной машины Java

1. [Scala. Запуск приложений Play Framework в облачных службах Azure](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### Типы, виды установки и обновления пакетов SDK
4. [Пакет SDK для управления службами Azure: Java](http://dl.windowsazure.com/javadoc/)
5. [Пакет SDK для управления службами Azure: Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Пакет SDK для управления службами Azure: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [Практическое руководство. Установка Ruby on Rails](virtual-machines-ruby-rails-web-app-linux.md)
6. [Пакет SDK для управления службами Azure: Python](https://github.com/Azure/azure-sdk-for-python)
    - [Практическое руководство. Веб-приложение Hello World на Django (Mac и Linux)](virtual-machines-python-django-web-app-linux.md)
7. [Пакет SDK для управления службами Azure: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Пакет SDK для управления службами Azure: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [Практическое руководство. Установка стека LAMP на виртуальной машине Azure](virtual-machines-linux-install-lamp-stack.md)
    - [Видео. Установка стека LAMP на виртуальной машине Azure](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Пакет SDK для управления службами Azure: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Блог. Mono, ASP.NET 5, Linux и Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## Примеры и сценарии

Просмотрите этот раздел для быстрой справки. При наличии предложений отправьте нам их в запросе на получение или напишите их в комментариях ниже.

1. [Развертывание нескольких виртуальных машин с помощью интерфейса командной строки Azure](virtual-machines-create-multi-vm-deployment-../xplat-cli-install.md)
2. [Репозиторий Github Azure Linux Патрика Чейнзона (Patrick Chanezon)](https://github.com/chanezon/azure-linux)
3. [Видео. Перемещение локальных данных USB из Linux в Azure с помощью **usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Видео. Доступ к графическому интерфейсу на базе Linux в Azure в браузере с помощью fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Видео. Общее хранилище на базе Linux с использованием предварительной версии Azure Files — часть 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Видео. Использование устройств Linux в Azure с помощью служебной шины и веб-сайтов](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Видео. Подключение собственного кэшированного в памяти приложения на основе Linux к Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Видео. Балансировка нагрузки для служб высокой доступности Linux в Azure: OpenLDAP и MySQL](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## Данные

Этот раздел содержит сведения о нескольких разных подходах к хранению и соответствующих технологиях, включая NoSQL, реляционные данные и большие объемы данных.

### NoSQL

1. [Блог. 8 баз данных NoSql с открытым исходным кодом для Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech). Опыт работы с CouchDb в Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Блог. Запуск CouchDB как службы с помощью node.js, CORS и Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [Практическое руководство. Создание приложения Node.js в Azure с MongoDB с помощью надстройки MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb.md)
4. Cassandra
    - [Практическое руководство. Запуск Cassandra под управлением Linux в Azure и доступ к нему из Node.js](virtual-machines-linux-nodejs-running-cassandra.md)
5. Redis
    - [Блог. Redis на базе Windows в службе кэша Azure Redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Блог. Анонс поставщика состояний сеансов ASP.NET для предварительной версии Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Блог. RavenHQ теперь доступен в Azure Marketplace](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Данные большого размера
2. Hadoop/Cloudera  
	- [Блог. Установка Hadoop на виртуальных машинах Linux в Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [Практическое руководство. Приступая к работе с Hadoop и Hive с помощью HDInsight](hdinsight-get-started.md)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) — полностью управляемая служба Hadoop в Azure.

### Реляционная база данных
2. MySQL
    - [Практическое руководство. Установка и запуск MySQL](virtual-machines-linux-mysql-use-opensuse.md)
    - [Практическое руководство. Оптимизация производительности MySQL в Azure](virtual-machines-linux-optimize-mysql-perf.md)
    - [Практическое руководство. Кластеры MySQL](virtual-machines-linux-mysql-cluster.md)
    - [Практическое руководство. Создание базы данных MySQL в Marketplace](store-php-create-mysql-database.md)
    - [Практическое руководство. Django и MySQL на базе веб-сайтов Azure с использованием Python и Visual Studio](web-sites-python-ptvs-django-mysql.md)
    - [Практическое руководство. PHP и MySQL на базе веб-сайтов Azure с использованием WebMatrix](web-sites-php-mysql-use-webmatrix.md)
    - [Архитектура высокой доступности MySQL в Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Практическое руководство. Создание кластера MariaDbs с несколькими источниками](virtual-machines-mariadb-cluster.md)
8. [Установка Postgres с использованием corosync и pg\_bouncer с помощью ILB](https://github.com/chgeuer/postgres-azure)


## Проверка подлинности и шифрование

Проверка подлинности и шифрование играют важную роль в разработке программного обеспечения, и в Интернете существует множество разделов, помогающих освоить и использовать соответствующие методики обеспечения безопасности. Мы приводим несколько основных принципов использования, позволяющих быстро запустить Linux и начать обработку рабочих нагрузок с открытым кодом, а также упоминаем средства для сброса или удаления компонентов удаленной безопасности в Azure. Это основные процедуры. В будущем будут добавлены более сложные сценарии.

4. [Основные сведения. Использование сертификатов и управление ими](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Основные сведения. SSH](virtual-machines-linux-use-ssh-key.md)
8. [Основные сведения. Сброс пароля или свойств SSH для Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [Основные сведения. Использование прав корневой учетной записи](virtual-machines-linux-use-root-privileges.md)

## Linux для высокопроизводительных вычислений (HPC)

Запуск высокопроизводительных рабочих нагрузок на кластерах виртуальных машин Linux, построенных с помощью средств с открытым исходным кодом или пакета Microsoft HPC.

1.	Шаблон быстрого запуска [Развертывание кластера SLURM](http://azure.microsoft.com/documentation/templates/slurm/) и [запись в блоге](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
2.	[Шаблон быстрого запуска: развертывание кластера Torque](http://azure.microsoft.com/documentation/templates/torque-cluster/)
3.	[Шаблон краткого руководства: создание кластера HPC с вычислительными узлами Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[Учебник: начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-cluster-hpcpack.md)
5.	[Учебник: запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-cluster-hpcpack-namd.md)
6.	[Учебник: настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-cluster-rdma.md)


## Разработка, управление и оптимизация

Этот раздел начинается с записи блога, содержащей несколько видеороликов на странице [Видео. Виртуальные машины Azure: использование Chef, Puppet и Docker для управления виртуальными машинами Linux](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Однако мир разработки, управления и оптимизации очень быстро изменяется, поэтому используйте следующий перечень лишь в качестве отправной точки.

1. Docker
	- [Расширение виртуальных машин Docker для Linux в Azure](virtual-machines-docker-vm-extension.md)
	- [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)](virtual-machines-docker-with-../xplat-cli-install.md)
	- [Использование расширения виртуальных машин Docker на портале предварительной версии Azure](virtual-machines-docker-with-portal.md)
	- [Как быстро приступить к работе с Docker в Azure Marketplace](virtual-machines-docker-ubuntu-quickstart.md)
	- [Использование docker-machine в Azure](virtual-machines-docker-machine.md)
	- [Как использовать Docker со Swarm в Azure](virtual-machines-docker-swarm.md)
	- [Начало работы с решениями Docker и Compose в Azure](virtual-machines-docker-compose-quickstart.md)

2. [Использование Fleet в CoreOS](virtual-machines-linux-coreos-how-to.md)
3. Deis
	- [Репозиторий GitHub. Установка Deis на кластере CoreOS в Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Полное руководство по автоматизированному развертыванию кластера Kubernetes с помощью CoreOS и Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins and Hudson
	- [Блог. Подключаемый модуль ведомого режима Jenkins для Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Репозиторий GitHub. Подключаемый модуль хранилища Jenkins для Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Сторонние решения. Подключаемый модуль ведомого режима Hudson для Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Сторонние решения. Подключаемый модуль хранилища Hudson для Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef и виртуальные машины](virtual-machines-windows-install-chef-client.md)
	- [Видео. Что такое Chef и как это работает?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Служба автоматизации Azure
	- [Видео. Использование службы автоматизации Azure для виртуальных машин Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Powershell DSC для Linux
    - [Блог. Как сделать Powershell DSC для Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [Github. DSC клиента Docker](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Подключаемый модуль Packer для Azure](https://github.com/msopentech/packer-azure)

## Поддержка, устранение неполадок и описание проблемных ситуаций

1. Посвященная поддержке документация корпорации Майкрософт
	- [Поддержка. Поддержка образов Linux в Microsoft Azure](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md

<!---HONumber=Oct15_HO1-->