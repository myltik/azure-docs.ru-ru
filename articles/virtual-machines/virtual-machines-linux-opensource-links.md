---
title: "Linux и вычисления с открытым кодом в Azure | Документация Майкрософт"
description: "Содержит перечень тем по Linux и вычислениям с открытым кодом в Azure, включая базовые принципы использования Linux, некоторые основные понятия о запуске или отправке образов Linux в Azure, а также другие сведения о конкретных технологиях и оптимизациях."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: a7e608b5-26ea-41e0-b46b-1a483a257754
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/27/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 3136b8345d0c851c29a9498089da73c8564549d1
ms.openlocfilehash: 3db8a2b4618c05430a7e0cd7664514abc582078b
ms.lasthandoff: 01/31/2017


---
# <a name="linux-and-open-source-computing-on-azure"></a>Linux и вычисления с открытым кодом в Azure
В этой статье перечислена вся документация, необходимая для создания виртуальных машин Linux с помощью классической модели развертывания и управления ими.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

## <a name="get-started"></a>Начало работы
* [Общая информация о Linux в Azure](virtual-machines-linux-intro-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Вопросы и ответы по виртуальным машинам Azure, созданным при помощи классической модели развертывания](virtual-machines-linux-classic-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Образы виртуальных машин](virtual-machines-linux-classic-about-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Передача собственного образа дистрибутива](virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) (а также инструкции по использованию [рекомендованных дистрибутивов Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))
* [Вход на виртуальную машину Linux через классический портал Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="set-up"></a>Настройка
* [Установка интерфейса командной строки Azure (Azure CLI)](../xplat-cli-install.md)

## <a name="tutorials"></a>Учебники
* [Установка стека LAMP на виртуальную машину Linux в Azure](virtual-machines-linux-create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Веб-приложение Ruby on Rails на виртуальной машине Azure](linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)
* [Практическое руководство. Установка Apache Qpid Proton-C для AMQP и служебной шины](../service-bus-messaging/service-bus-amqp-apache.md)

### <a name="databases"></a>Базы данных
* [Оптимизация производительности MySQL в Azure](virtual-machines-linux-classic-optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Кластеры MySQL](virtual-machines-linux-classic-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Запуск Cassandra под управлением Linux в Azure и доступ к нему из Node.js](virtual-machines-linux-classic-cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Создание кластера MariaDbs с несколькими источниками](virtual-machines-linux-classic-mariadb-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="hpc"></a>HPC
* [Начало работы с вычислительными узлами Linux в кластере пакета HPC в Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Запуск NAMD с пакетом Microsoft HPC на вычислительных узлах Linux в Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Настройка кластера Linux RDMA для выполнения приложений MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="docker"></a>Docker
* [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)](virtual-machines-linux-classic-cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Использование расширения виртуальных машин Docker на портале Azure](virtual-machines-linux-classic-portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Использование docker-machine в Azure](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="ubuntu"></a>Ubuntu
* [Практическое руководство. Кластеры MySQL](virtual-machines-linux-classic-mysql-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Практическое руководство. Node.js и Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="opensuse"></a>OpenSUSE
* [Практическое руководство. Установка и запуск MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="coreos"></a>CoreOS
* [Практическое руководство. Использование CoreOS в Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="planning"></a>Планирование
* [Руководство по реализации служб инфраструктуры Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Выбор имен пользователей Linux](virtual-machines-linux-usernames.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Как настроить группу доступности для виртуальных машин в классической модели развертывания](virtual-machines-linux-classic-configure-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Как запланировать плановое обслуживание на виртуальных машинах Azure](virtual-machines-linux-planned-maintenance-schedule.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Управление доступностью виртуальных машин](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Плановое обслуживание виртуальных машин Linux в Azure](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="deployment"></a>Развертывание
* [Создание настраиваемой виртуальной машины под управлением Linux](virtual-machines-linux-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Основные сведения. Запись виртуальной машины Linux для создания шаблона](virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Информация о нерекомендованных дистрибутивах](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="management"></a>управления
* [SSH](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Сброс пароля или свойств SSH для Linux](virtual-machines-linux-classic-reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Использование прав root](virtual-machines-linux-use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-resources"></a>Ресурсы Azure
* [Агент Linux для Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Расширения и компоненты виртуальных машин Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Включение пользовательских данных в виртуальную машину Azure для использования с Cloud-init](virtual-machines-windows-classic-inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>Хранилище
* [Подключение диска данных к виртуальной машине Linux](virtual-machines-linux-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Отключение диска данных от виртуальной машины Linux](virtual-machines-linux-classic-detach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Сеть
* [Как настроить конечные точки в классической виртуальной машине в Azure](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="troubleshooting"></a>Устранение неполадок
* [Устранение неполадок с подключением Secure Shell (SSH) к виртуальной машине Azure под управлением Linux](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Устранение неполадок в классическом развертывании во время создания виртуальной машины Linux в Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)  
* [Устранение неполадок в классическом развертывании при перезагрузке или изменении размера существующей виртуальной машины Linux в Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 

## <a name="reference"></a>Справочные материалы
* [Команды Azure CLI в режиме управления службами Azure (ASM)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Интерфейс API REST управления службой Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)

## <a name="general-links"></a>Общие ссылки
Ниже представлены ссылки на блоги Microsoft, документы Technet и внешние веб-сайты, а не на статьи Azure, которые приведены выше. Так как и платформа Azure, и мир вычислений с открытым кодом активно развиваются, можно с большой вероятностью утверждать, что эти ссылки устарели, *хотя* мы стараемся постоянно добавлять новые разделы и удалять неактуальные. Если мы что-то упустили, напишите об этом в комментариях или направьте запрос на получение в наш [репозиторий Github](https://github.com/Azure/azure-content/).

* [Запуск ASP.NET 5 в Linux с помощью контейнеров Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
* [Развертывание образа виртуальной машины CentOS из OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
* [Инфраструктура обновления SUSE](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
* [SUSE Linux Enterprise Server для SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
* [Построение высокодоступной системы Linux в Azure за 12 шагов](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
* [Автоматизация подготовки Linux в Azure с помощью интерфейса командной строки Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
* [Файловая система Gluster в Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### <a name="freebsd"></a>FreeBSD
* [Запуск FreeBSD в Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
* [Простое развертывание FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
* [Развертывание пользовательского образа FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
* [Kaspersky AV для файлового сервера Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a name="nosql"></a>NoSQL
* [8 баз данных NoSql с открытым исходным кодом для Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
* [Slideshare (MSOpenTech). Опыт работы с CouchDb в Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
* [Запуск CouchDB как службы с помощью node.js, CORS и Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
* [Redis на базе Windows в службе кэша Azure Redis](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
* [Анонс поставщика состояний сеансов ASP.NET для предварительной версии Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
* [Блог. RavenHQ теперь доступен в Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### <a name="big-data"></a>Данные большого размера
* [Установка Hadoop на виртуальных машинах Linux в Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
* [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### <a name="relational-database"></a>Реляционная база данных
* [Архитектура высокой доступности MySQL в Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
* [Установка Postgres с использованием corosync и pg_bouncer с помощью ILB](https://github.com/chgeuer/postgres-azure)

### <a name="linux-high-performance-computing-hpc"></a>Linux для высокопроизводительных вычислений (HPC)
* [Шаблон для быстрого развертывание кластера SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) и [запись в блоге](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [Шаблон краткого руководства: создание кластера HPC с вычислительными узлами Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### <a name="devops-management-and-optimization"></a>Разработка, управление и оптимизация
Так как мир разработки, управления и оптимизации очень быстро изменяется, используйте следующий список лишь в качестве отправной точки.

* [Видео. Виртуальные машины Azure: использование Chef, Puppet и Docker для управления виртуальными машинами Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)
* [Полное руководство по автоматизированному развертыванию кластера Kubernetes с помощью CoreOS и Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
* [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Подключаемый модуль ведомого режима Jenkins для Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
* [Репозиторий GitHub. Подключаемый модуль хранилища Jenkins для Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
* [Сторонние решения. Подключаемый модуль ведомого режима Hudson для Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
* [Сторонние решения. Подключаемый модуль хранилища Hudson для Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Видео. Что такое Chef и как это работает?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)
* [Видео. Использование службы автоматизации Azure для виртуальных машин Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* [Блог. Как сделать Powershell DSC для Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
* [Github. DSC клиента Docker](https://github.com/anweiss/DockerClientDSC)
* [Подключаемый модуль Packer для Azure](https://github.com/msopentech/packer-azure)


