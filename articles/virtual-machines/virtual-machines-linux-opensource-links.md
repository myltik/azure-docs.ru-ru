<properties
	pageTitle="Linux и вычисления с открытым кодом в Azure | Microsoft Azure"
	description="Содержит перечень тем по Linux и вычислениям с открытым кодом в Azure, включая базовые принципы использования Linux, некоторые основные понятия о запуске или отправке образов Linux в Azure, а также другие сведения о конкретных технологиях и оптимизациях."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/26/2016"
	ms.author="rasquill"/>



# Linux и вычисления с открытым кодом в Azure

В этом документе предпринята попытка собрать в одном месте список всех разделов, написанных сотрудниками и партнерами корпорации Майкрософт о запуске виртуальных машин под управлением Linux, а также других открытых вычислительных сред и приложений на платформе Microsoft Azure на основе классической модели развертывания.

Так как и платформа Azure, и мир вычислений с открытым исходным кодом активно развиваются, можно с большой вероятностью утверждать, что этот документ уже устарел, *хотя* мы стараемся постоянно добавлять новые разделы и удалять неактуальные. Если мы что-то упустили, напишите об этом в комментариях или направьте запрос на получение в наш [репозиторий Github](https://github.com/Azure/azure-content/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Общие примечания
Разделы расположены в правой части этой страницы. (Ссылки могут указываться в нескольких подразделах, так как разделы могут охватывать несколько концепций, дистрибутивов или технологий.) Кроме того, существует несколько разделов, описывающих различные параметры Linux и репозитории образов, которые содержат примеры и инструкции по отправке пользовательских образов.

- [Azure Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Мероприятия и демонстрации. Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Практическое руководство. Передача собственного образа дистрибутива](virtual-machines-linux-classic-create-upload-vhd.md) (а также инструкции по использованию [рекомендованных дистрибутивов Azure](virtual-machines-linux-endorsed-distros.md))
- [Примечания. Общие требования к Linux для выполнения в Azure](virtual-machines-linux-create-upload-generic.md)
- [Примечания. Введение в Linux в Azure](virtual-machines-linux-intro-on-azure.md)

## Дистрибутивы

Существует множество дистрибутивов Linux, которые обычно упорядочены по системам управления пакетами: некоторые основаны на dpgk, например Debian и Ubuntu, другие — на rpm, например CentOS, SUSE и RedHat. Некоторые компании, являющиеся официальными партнерами корпорации Майкрософт, предоставляют образы дистрибутивов, которые включаются в список рекомендуемых. Другие предоставляются сообществом. Приведенным в этом разделе дистрибутивам посвящены официальные статьи, даже если они использовались только в примерах к другим технологиям.

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu — это очень популярный и рекомендуемый для Azure дистрибутив Linux на основе управления пакетами dkpg и apt-get.

3. [Практическое руководство. Кластеры MySQL](virtual-machines-linux-classic-mysql-cluster.md)
4. [Практическое руководство. Node.js и Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
6. [Углубленные знания. Запуск ASP.NET 5 в Linux с помощью контейнеров Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### CentOS

Дистрибутив CentOS Linux — это стабильная, предсказуемая, управляемая и воспроизводимая платформа, созданная на основе исходного кода Red Hat Enterprise Linux (RHEL).

4. [Блог. Развертывание образа виртуальной машины CentOS из OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Практическое руководство. Установка Apache Qpid Proton-C для AMQP и служебной шины](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server и openSUSE

11. [Практическое руководство. Установка и запуск MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
13. [[Форум по SUSE] Практическое руководство. Переход на новый сервер исправлений](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Образы. SUSE Linux Enterprise Server для SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS — это небольшой оптимизированный дистрибутив, который ориентирован исключительно на вычисления и допускает гибкую настройку управления.

10. [Коллекция образов](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [Практическое руководство. Использование CoreOS в Azure](virtual-machines-linux-classic-coreos-howto.md)
12. [Практическое руководство. Начало работы с Fleet и Docker на CoreOS в Azure](virtual-machines-linux-classic-coreos-fleet-get-started.md)


### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Блог. Запуск FreeBSD в Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Блог. Простое развертывание FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Блог. Развертывание пользовательского образа FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
18. [Marketplace. Kaspersky AV для файлового сервера Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Основные сведения

1. [Основные сведения. Интерфейс командной строки Azure (CLI Azure)](../xplat-cli-install.md)
5. [Основные сведения. Выбор имен пользователей Linux](virtual-machines-linux-usernames.md)
6. [Основные сведения. Вход на виртуальную машину Linux через классический портал Azure](virtual-machines-linux-classic-log-on.md)
7. [Основные сведения. SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Основные сведения. Сброс пароля или свойств SSH для Linux](virtual-machines-linux-classic-reset-access.md)
9. [Основные сведения. Использование прав корневой учетной записи](virtual-machines-linux-use-root-privileges.md)
10. [Основные сведения. Подключение диска данных к виртуальной машине Linux](virtual-machines-linux-classic-attach-disk.md)
11. [Основные сведения. Отключение диска данных от виртуальной машины Linux](virtual-machines-linux-classic-detach-disk.md)
12. [Блог основных сведений. Оптимизация хранилища, дисков и производительности с помощью Linux и Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Основные сведения. RAID](virtual-machines-linux-configure-raid.md)
14. [Основные сведения. Запись виртуальной машины Linux для создания шаблона](virtual-machines-linux-classic-capture-image.md)
15. [Основные сведения. Агент Linux для Azure](virtual-machines-linux-agent-user-guide.md)
16. [Основные сведения. Расширения и компоненты виртуальных машин Azure](virtual-machines-windows-extensions-features.md)
17. [Основные сведения. Включение пользовательских данных в виртуальную машину для использования с пакетом Cloud-init](virtual-machines-windows-classic-inject-custom-data.md)
18. [Блог основных сведений. Сборка высокодоступной системы Linux в Azure за 12 шагов](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Блог основных сведений. Автоматизация подготовки Linux в Azure с помощью CLI Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
23. [Справочник по API-интерфейсу управления службами REST](https://msdn.microsoft.com/library/azure/ee460799.aspx)
24. [Файловая система Gluster в Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Образы и репозитории сообщества
4. [GitHub](https://github.com/Azure/) — для CLI Azure и множества других средств и проектов.
5. [Docker Hub Registry](https://registry.hub.docker.com/) — реестр для образов контейнеров Docker.

## Данные

Этот раздел содержит сведения о нескольких разных подходах к хранению и соответствующих технологиях, включая NoSQL, реляционные данные и большие объемы данных.

### NoSQL

1. [Блог. 8 баз данных NoSql с открытым исходным кодом для Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech). Опыт работы с CouchDb в Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Блог. Запуск CouchDB как службы с помощью node.js, CORS и Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
4. Cassandra
    - [Практическое руководство. Запуск Cassandra под управлением Linux в Azure и доступ к нему из Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [Блог. Redis на базе Windows в службе кэша Azure Redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Блог. Анонс поставщика состояний сеансов ASP.NET для предварительной версии Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Блог. RavenHQ теперь доступен в Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Данные большого размера
2. Hadoop/Cloudera  
	- [Блог. Установка Hadoop на виртуальных машинах Linux в Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) — полностью управляемая служба Hadoop в Azure.

### Реляционная база данных
2. MySQL
    - [Практическое руководство. Установка и запуск MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [Практическое руководство. Оптимизация производительности MySQL в Azure](virtual-machines-linux-classic-optimize-mysql.md)
    - [Практическое руководство. Кластеры MySQL](virtual-machines-linux-classic-mysql-cluster.md)
    - [Архитектура высокой доступности MySQL в Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Практическое руководство. Создание кластера MariaDbs с несколькими источниками](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [Установка Postgres с использованием corosync и pg\_bouncer с помощью ILB](https://github.com/chgeuer/postgres-azure)


## Проверка подлинности и шифрование

Проверка подлинности и шифрование играют важную роль в разработке программного обеспечения, и в Интернете существует множество разделов, помогающих освоить и использовать соответствующие методики обеспечения безопасности. Мы приводим несколько основных принципов использования, позволяющих быстро запустить Linux и начать обработку рабочих нагрузок с открытым кодом, а также упоминаем средства для сброса или удаления компонентов удаленной безопасности в Azure. Это основные процедуры. В будущем будут добавлены более сложные сценарии.

4. [Основные сведения. Использование сертификатов и управление ими](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Основные сведения. SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Основные сведения. Сброс пароля или свойств SSH для Linux](virtual-machines-linux-classic-reset-access.md)
9. [Основные сведения. Использование прав корневой учетной записи](virtual-machines-linux-use-root-privileges.md)

## Linux для высокопроизводительных вычислений (HPC)

Запуск высокопроизводительных рабочих нагрузок на кластерах виртуальных машин Linux, построенных с помощью средств с открытым исходным кодом или пакета Microsoft HPC.

1.	[Шаблон быстрого запуска: развертывание кластера SLURM](https://azure.microsoft.com/documentation/templates/slurm/) и [запись в блоге](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
3.	[Шаблон краткого руководства: создание кластера HPC с вычислительными узлами Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[Учебник: начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[Учебник: запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[Учебник: настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md)


## Разработка, управление и оптимизация

Этот раздел начинается с записи блога, содержащей несколько видеороликов на странице [Видео. Виртуальные машины Azure: использование Chef, Puppet и Docker для управления виртуальными машинами Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Однако мир разработки, управления и оптимизации очень быстро изменяется, поэтому используйте следующий перечень лишь в качестве отправной точки.

1. Docker
	- [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)](virtual-machines-linux-classic-cli-use-docker.md)
	- [Использование расширения виртуальных машин Docker на портале Azure](virtual-machines-linux-classic-portal-use-docker.md)
    - [Как быстро приступить к работе с Docker в Azure Marketplace](virtual-machines-linux-classic-docker-quickstart.md)
	- [Использование docker-machine в Azure](virtual-machines-linux-classic-docker-machine.md)

2. [Использование Fleet в CoreOS](virtual-machines-linux-classic-coreos-howto.md)
4. Kubernetes
	- [Полное руководство по автоматизированному развертыванию кластера Kubernetes с помощью CoreOS и Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins and Hudson
	- [Блог. Подключаемый модуль ведомого режима Jenkins для Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Репозиторий GitHub. Подключаемый модуль хранилища Jenkins для Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Сторонние решения. Подключаемый модуль ведомого режима Hudson для Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Сторонние решения. Подключаемый модуль хранилища Hudson для Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
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

<!---HONumber=AcomDC_0330_2016-->