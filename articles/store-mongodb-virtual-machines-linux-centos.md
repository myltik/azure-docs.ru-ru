<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Установка MongoDB" pageTitle="Установка MongoDB на виртуальной машине под управлением CentOS Linux в Azure" metaKeywords="Azure, MongoDB" description="Узнайте, как установить Mongo DB на виртуальную машину в Azure." metaCanonical="" services="" documentationCenter="" title="Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure" authors="" solutions="" manager="" editor="" />

# Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure

[MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом.  С помощью [портала управления Azure][AzurePortal] можно создать виртуальную машину под управлением CentOS Linux из коллекции образов.  Затем можно установить и настроить базу данных MongoDB на виртуальной машине.

Вы узнаете следующее:

- Как использовать портал управления для создания виртуальной машины под управлением CentOS Linux из коллекции.
- Как подключаться к виртуальной машине с использованием SSH или PuTTY.
- Как установить MongoDB на виртуальной машине.

## Подпишитесь на предварительную версию виртуальных машин

Для создания виртуальных машин необходимо будет подписаться на предварительную версию виртуальных машин Azure. Если у вас нет учетной записи Azure, вы также можете зарегистрировать бесплатную пробную учетную запись.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Создание виртуальной машины под управлением CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

##Установка и запуск MongoDB на виртуальной машине

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

##Сводка
В этом учебнике было показано, как создать виртуальную машину под управлением Linux и удаленно подключаться к ней с помощью SSH или PuTTY.  Вы также узнали, как установить и настроить MongoDB на виртуальной машине Linux.  Дополнительные сведения о MongoDB см. в [документации по MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

