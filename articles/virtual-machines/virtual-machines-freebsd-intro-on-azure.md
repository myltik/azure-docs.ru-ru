---
title: "Введение в FreeBSD в Azure | Документация Майкрософт"
description: "Узнайте о том, как использовать виртуальные машины FreeBSD в Azure."
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2017
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 71ad04b10bc49500197db6fecdcc0305a1ea0dd2
ms.openlocfilehash: ecb1c385de6c1b12674326afe7d5a0ebf6cd9ad0


---
# <a name="introduction-to-freebsd-on-azure"></a>Введение в FreeBSD в Azure
В этом разделе представлен обзор запуска виртуальной машины FreeBSD в Azure.

## <a name="overview"></a>Обзор
FreeBSD для Microsoft Azure — это расширенная операционная система, которая использовалась для управления мощными современными серверами, настольными компьютерами и внедренными платформами.

Корпорация Майкрософт предоставляет образы FreeBSD на платформе Azure с предварительно настроенным [гостевым агентом виртуальной машины Azure](https://github.com/Azure/WALinuxAgent/). В настоящее время она предлагает образы FreeBSD следующих версий:

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE

Этот агент отвечает за обмен данными между виртуальной машиной FreeBSD и Azure Fabric при таких операциях, как подготовка виртуальной машины к первому использованию (имя пользователя, пароль или ключ SSH, имя узла и т. д.) и выборочное включение функций расширений виртуальной машины.

Стратегия в отношении текущей и будущих версий FreeBSD — обеспечивать актуальность и предоставлять последние версии вскоре после их публикации командой технических специалистов по выпуску FreeBSD.

## <a name="deploying-a-freebsd-virtual-machine"></a>Развертывание виртуальной машины FreeBSD
При использовании образа из Azure Marketplace развертывание виртуальной машины FreeBSD осуществляется просто.

- [FreeBSD 10.3 на Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 на Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)

## <a name="vm-extensions-for-freebsd"></a>Расширения виртуальной машины для FreeBSD
Ниже приведены поддерживаемые во FreeBSD расширения виртуальной машины.

### <a name="vmaccess"></a>VMAccess
Возможности расширения [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) :

* сброс пароля исходного пользователя sudo;
* создание пользователя sudo с указанным паролем;
* настройка заданного значения открытого ключа узла;
* сброс открытого ключа узла, указанного во время подготовки виртуальной машины, если не указан ключ узла;
* открытие порта SSH (22) и восстановление sshd_config, если для reset_ssh задано значение true;
* удаление существующего пользователя;
* проверка дисков;
* восстановление добавленного диска.

### <a name="customscript"></a>CustomScript
Возможности расширения [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) :

* скачивание предоставленных пользовательских сценариев из службы хранилища Azure или общедоступного внешнего хранилища (например, Github);
* выполнение сценария точки входа;
* поддержка встроенных команд;
* автоматическое преобразование символа новой строки в стиле Windows в сценариях оболочки и Python;
* автоматическое удаление спецификации в сценариях оболочки и Python;
* защита конфиденциальных данных в commandToExecute.

[!NOTE]В настоящий момент виртуальная машина FreeBSD поддерживает только CustomScript версии 1.x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Аутентификация: имена пользователей, пароли и ключи SSH
При создании виртуальной машины FreeBSD с помощью портала Azure необходимо указать имя пользователя, пароль или открытый ключ SSH.
Имена пользователей для развертывания виртуальной машины FreeBSD в Azure не должны совпадать с именами системных учетных записей (UID <&100;), уже присутствующих в виртуальной машине (например, root).
В настоящее время поддерживается только ключ SSH на основе RSA. Многострочный ключ SSH должен начинаться с `---- BEGIN SSH2 PUBLIC KEY ----` и заканчиваться `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Получение привилегий суперпользователя
Учетная запись пользователя, указанная во время развертывания экземпляра виртуальной машины в Azure, является привилегированной учетной записью. Пакет sudo установлен в опубликованном образе FreeBSD.
После входа в систему с помощью этой учетной записи пользователя вы сможете выполнять команды от учетной записи root, используя соответствующий синтаксис команд.

    $ sudo <COMMAND>

При необходимости можно получить доступ к оболочке root с помощью команды `sudo -s`.

## <a name="known-issues"></a>Известные проблемы
1. В настоящий момент не решены проблемы с FreeBSD 11.0 на Hyper-V (и Azure), из-за которых происходит сбой при загрузке виртуальной машины, в операционной системе которой установлено исправление `freebsd-update`. [Предложенное исправление](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=212721) включено в состав образов FreeBSD в Azure Marketplace. Команда FreeBSD не объединила его с исходным выпуском, поэтому выполнение `freebsd-update` приведет к замене ядра неисправленной версией. При использовании платформы Azure рекомендуется не устанавливать исправления для FreeBSD 11.0, пока исправление не будет опубликовано как ERRATA.

2. [Гостевой агент виртуальной машины Azure](https://github.com/Azure/WALinuxAgent/) версии 2.2.2 имеет [известную проблему] (https://github.com/Azure/WALinuxAgent/pull/517), вызывающую сбой подготовки виртуальной машины FreeBSD в Azure. Пользователям виртуальной машины FreeBSD в Azure рекомендуется использовать версию 2.2.1 или более раннюю. Ошибка будет исправлена в [гостевом агенте виртуальной машины Azure](https://github.com/Azure/WALinuxAgent/) версии 2.2.3. 

## <a name="next-steps"></a>Дальнейшие действия
* Перейдите на сайт [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) , чтобы создать виртуальную машину FreeBSD.
* Если вы хотите перенести свою собственную виртуальную машину FreeBSD в Azure, ознакомьтесь с разделом [Создание и отправка виртуального жесткого диска FreeBSD в Azure](./virtual-machines-linux-classic-freebsd-create-upload-vhd.md).



<!--HONumber=Jan17_HO2-->


