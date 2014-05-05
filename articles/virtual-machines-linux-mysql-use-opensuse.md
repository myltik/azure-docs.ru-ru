<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Установка MongoDB" pageTitle="Установка MongoDB на виртуальной машине под управлением CentOS Linux в Azure" metaKeywords="Azure, MongoDB" description="Узнайте, как установить Mongo DB на виртуальную машину в Azure." metaCanonical="" services="" documentationCenter="" title="Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure" authors="" solutions="" manager="" editor="" />

# Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure

[MySQL][MySQL] является популярной базой данных SQL с открытым исходным кодом.  С помощью [портала управления Azure][AzurePortal] можно создать виртуальную машину под управлением OpenSUSE Linux из коллекции образов.  Затем можно установить и настроить базу данных MySQL на виртуальной машине.

В этом руководстве вы узнаете следующее:

- Как использовать портал управления для создания виртуальной машины OpenSUSE Linux из коллекции.
- Как подключаться к виртуальной машине с использованием SSH или PuTTY.
- Как установить MySQL на виртуальной машине.

## Подпишитесь на предварительную версию виртуальных машин

Для создания виртуальных машин необходимо будет подписаться на предварительную версию виртуальных машин Azure. Если у вас нет учетной записи Azure, вы также можете зарегистрировать бесплатную пробную учетную запись.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Создайте виртуальную машину под управлением OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Установите и запустите MySQL на виртуальной машине

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Сводка
В этом учебнике было показано, как создать виртуальную машину под управлением OpenSUSE Linux и удаленно подключаться к ней с помощью SSH или PuTTY.  Вы также узнали, как установить и настроить MySQL на виртуальной машине Linux.  Дополнительные сведения о MySQL см. в [документации по MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

