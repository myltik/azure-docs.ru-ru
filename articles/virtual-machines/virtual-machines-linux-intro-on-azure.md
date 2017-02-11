---
title: "Введение в Linux в Azure | Документация Майкрософт"
description: "Узнайте о том, как использовать виртуальные машины Linux в Azure."
services: virtual-machines-linux
documentationcenter: python
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: b13bf305-87bf-4df3-815e-e8f6337aa6ea
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: dc61f74a10fed1ba9e3959326e0020cf2b4440ea


---
# <a name="introduction-to-linux-on-azure"></a>Введение в Linux в Azure
В этом разделе содержится обзор некоторых аспектов использования виртуальных машин Linux в облаке Azure. При использовании образа из коллекции развертывание виртуальной машины Linux осуществляется просто.

## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Проверка подлинности: имена пользователей, пароли и ключи SSH
При создании виртуальной машины Linux с помощью классического портала Azure вам будет предложено указать имя пользователя, пароль или открытый ключ SSH. К имени пользователя при развертывании виртуальной машины Linux на платформе Azure применяется следующее ограничение: запрещается использовать имена учетных записей (идентификатор пользователя <100), которые уже используются в виртуальной машине (например, root).

* Ознакомьтесь со статьей [Создание виртуальной машины с ОС Linux](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ознакомьтесь со статьей [Использование SSH с Linux в Azure](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="obtaining-superuser-privileges-using-sudo"></a>Получение привилегий суперпользователя с помощью `sudo`
Учетная запись пользователя, указанная во время развертывания экземпляра виртуальной машины в Azure, является привилегированной учетной записью. Эта учетная запись настраивается агентом Linux Azure, чтобы обеспечить возможность повысить привилегии до уровня root (учетная запись суперпользователя) с помощью служебной программы `sudo` . После входа в систему с помощью этой учетной записи пользователя вы сможете выполнять команды от учетной записи root, используя синтаксис команды

    # sudo <COMMAND>

При необходимости можно получить доступ к оболочке root с помощью команды **sudo -s**.

* Ознакомьтесь со статьей [Использование прав корневой учетной записи на виртуальных машинах Linux в Azure](virtual-machines-linux-use-root-privileges.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="firewall-configuration"></a>Настройка брандмауэра
Azure предоставляет фильтр входящих пакетов, который ограничивает возможности подключения к портам, указанным на классическом портале Azure. По умолчанию разрешен только порт SSH. Можно открыть доступ к дополнительным портам на вашей виртуальной машине Linux, настроив конечные точки на классическом портале Azure:

* Ознакомьтесь со статьей [Настройка конечных точек виртуальной машины](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Образы Linux в коллекции Azure не включают брандмауэр *iptables* по умолчанию. При необходимости этот брандмауэр можно настроить для предоставления дополнительных возможностей фильтрации.

## <a name="hostname-changes"></a>Изменения имени узла
При начальном развертывании экземпляра образа Linux необходимо указать имя узла для виртуальной машины. После запуска виртуальной машины это имя узла публикуется на DNS-серверах платформы, чтобы несколько соединенных между собой виртуальных машин могли выполнять поиск IP-адресов с помощью имен узлов.

При необходимости изменения имени узла после развертывания виртуальной машины рекомендуется использовать команду

    # sudo hostname <newname>

Агент Linux Azure предоставляет функции автоматического обнаружения изменения имени узла и соответствующей настройки виртуальной машины для сохранения этого изменения и его публикации на DNS-серверах платформы.

* [Руководство пользователя агента Linux для Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="cloud-init"></a>Cloud-Init
Образы **Ubuntu** и **CoreOS** используют пакет cloud-init в Azure, предоставляющий дополнительные возможности для начальной загрузки виртуальной машины.

* [Включение пользовательских данных в виртуальную машину Azure](virtual-machines-windows-classic-inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Пользовательские данные и cloud-init в Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
* [Создание разделов подкачки Azure с помощью cloud-init](https://wiki.ubuntu.com/AzureSwapPartitions)
* [Использование CoreOS в Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)

## <a name="virtual-machine-image-capture"></a>Запись образа виртуальной машины
Azure предоставляет возможность записи состояния существующей виртуальной машиной в образ, который впоследствии можно использовать для развертывания дополнительных экземпляров виртуальной машины. Агент Linux Azure может использоваться для отката некоторых настроек, которые были выполнены в процессе подготовки. Вы можете выполнить следующие действия, чтобы записать виртуальную машину в виде образа:

1. Выполните команду **waagent -deprovision** для отмены настройки подготовки. Или выполните команду **waagent -deprovision+user** при необходимости, чтобы удалить учетную запись пользователя, указанную во время подготовки, и все связанные с ней данные.
2. Остановите работу виртуальной машины или выключите ее.
3. Нажмите кнопку *Запись* на классическом портале Azure либо используйте средства Powershell или CLI, чтобы записать образ данной виртуальной машины.
   
   * Ознакомьтесь со статьей [Запись образа виртуальной машины Linux для использования в качестве шаблона](virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="attaching-disks"></a>Присоединение дисков
Каждая виртуальная машина также имеет подключенный временный локальный *диск ресурсов* . Так как данные на диске ресурсов могут плохо переносить перезагрузки, его часто используют приложения и процессы, выполняющиеся на виртуальной машине, для промежуточного и **временного** хранения данных. Он также используется для хранения страниц или замены файлов операционной системы.

В системе Linux диск ресурсов обычно управляется агентом Linux Azure, и он автоматически подключается к **/mnt/resource** (или **/mnt** на образах Ubuntu).

> [!NOTE]
> Следует отметить, что диск ресурсов **временный** , и его можно удалить и переформатировать при перезагрузке виртуальной машины.
> 
> 

В Linux диск данных можно назвать по ядру, например `/dev/sdc`, и пользователям придется выполнить разбиение на разделы, форматирование и подключение этого ресурса. Это действие подробно рассматривается в руководстве [Как присоединить диск данных к виртуальной машине Linux](virtual-machines-linux-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

* **См. также**: [Настройка программного RAID-массива в Linux](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) & [Настройка диспетчера логических томов на виртуальной машине Linux в Azure](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Nov16_HO3-->


