---
title: "Использование хранилища файлов Azure в Linux | Документация Майкрософт"
description: "Узнайте, как подключить общую папку Azure через протокол SMB с помощью Linux."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/8/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: d93c82b9c2e66c7241ddd579c1be74396174fd65
ms.contentlocale: ru-ru
ms.lasthandoff: 08/07/2017

---
# <a name="use-azure-file-storage-with-linux"></a>Использование хранилища файлов Azure в Linux
[Хранилище файлов Azure](storage-dotnet-how-to-use-files.md) — это простая в использовании облачная файловая система Майкрософт. Общие папки Azure можно подключить в дистрибутивах Linux с помощью [пакета cifs-utils](https://wiki.samba.org/index.php/LinuxCIFS_utils) из [проекта Samba](https://www.samba.org/). В этой статье описаны два способа подключения общей папки Azure: по запросу с помощью команды `mount` и при загрузке путем создания записи в `/etc/fstab`.

> [!NOTE]  
> Чтобы подключить общую папку Azure за пределами региона Azure, в котором она размещается, например локально или в другом регионе Azure, операционная система должна поддерживать протокол функций шифрования SMB 3.0. Функция шифрования протокола SMB 3.0 для Linux появилась в ядре версии 4.11. Эта функция позволяет подключать общую папку файлов Azure из локальной среды или другого региона Azure. На момент публикации этой статьи функция была добавлена в дистрибутивы Ubuntu 16.04 и более поздних версий.


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Предварительные требования для подключения общей папки Azure с помощью Linux и пакета cifs-utils
* **Выберите дистрибутив Linux, в который можно установить пакет cifs-utils**. Корпорация Майкрософт рекомендует следующие дистрибутивы Linux в коллекции образов Azure:

    * Ubuntu Server 14.04 или более поздней версии;
    * RHEL 7 или более поздней версии;
    * CentOS 7 или более поздней версии;
    * Debian 8;
    * openSUSE 13.2 или более поздней версии.
    * SUSE Linux Enterprise Server 12   

    > [!Note]  
    > С хранилищем файлов Azure можно использовать любой дистрибутив Linux, в котором установлены или скомпилированы последние версии пакета cifs-utils.

* <a id="install-cifs-utils"></a>**Установите пакет cifs-utils**. Пакет cifs-utils можно установить с помощью диспетчера пакетов в дистрибутиве Linux по своему усмотрению. 

    В дистрибутивах **Ubuntu** и **на основе Debian** используйте диспетчер пакетов `apt-get`:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    В **RHEL** и **CentOS** используйте диспетчер пакетов `yum`:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    В **openSUSE** используйте диспетчер пакетов `zypper`:

    ```
    sudo zypper install samba*
    ```

    В других дистрибутивах используйте соответствующий диспетчер пакетов или выполните [компиляцию из источника](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Выберите разрешения к каталогу и файлам подключаемой общей папки**. В приведенных ниже примерах используется разрешение 0777, чтобы все пользователи имели право на чтение, запись и выполнение файлов. При необходимости можно заменить его другим [разрешением chmod](https://en.wikipedia.org/wiki/Chmod). 

* **Имя учетной записи хранения**: чтобы подключить общую папку Azure, вам потребуется имя учетной записи хранения.

* **Ключ учетной записи хранения**: чтобы подключить общую папку Azure, вам потребуется первичный (или вторичный) ключ учетной записи хранения. В настоящее время ключи SAS для подключения не поддерживаются.

* **Откройте порт 445**. Взаимодействие SMB выполняется через TCP-порт 445. Проверьте, чтобы брандмауэр не блокировал TCP-порты 445 с клиентского компьютера.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Подключение общей папки Azure по требованию с помощью `mount`
1. **[Установите пакет cifs-utils для вашего дистрибутива Linux](#install-cifs-utils)**.

2. **Создайте папку для точки подключения**. Это можно сделать в любом месте файловой системы.

    ```
    mkdir mymountpoint
    ```

3. **Используйте команду mount для подключения общей папки Azure**. Не забудьте заменить значения `<storage-account-name>`, `<share-name>` и `<storage-account-key>` соответствующим образом.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Чтобы отключить общую папку Azure, выполните команду `sudo umount ./mymountpoint`.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Создание постоянной точки подключения для общей папки Azure с помощью `/etc/fstab`
1. **[Установите пакет cifs-utils для вашего дистрибутива Linux](#install-cifs-utils)**.

2. **Создайте папку для точки подключения**. Это можно сделать в любом месте файловой системы, но необходимо отметить абсолютный путь к папке. В следующем примере создается папка в корне.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Используйте следующую команду, чтобы добавить следующую строку в `/etc/fstab`**. Не забудьте заменить значения `<storage-account-name>`, `<share-name>` и `<storage-account-key>` соответствующим образом.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Вместо перезагрузки можно использовать `sudo mount -a`, чтобы подключить общую папку после изменения `/etc/fstab`.

## <a name="feedback"></a>Отзыв
Пользователи Linux, нам очень интересно ваше мнение!

Хранилище файлов Azure для группы пользователей Linux включает форум, где можно поделиться своим мнением и опытом адаптации хранилища файлов в Linux. Чтобы вступить в группу пользователей, отправьте электронное письмо в группу [Пользователи хранилища файлов Azure в Linux](mailto:azurefileslinuxusers@microsoft.com).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию о хранилище файлов Azure см. по этим ссылкам.
* [Справочник по REST API службы файлов](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Использование Azure PowerShell со службой хранилища Azure](storage-powershell-guide-full.md)
* [Перенос данных с помощью AzCopy для Windows](storage-use-azcopy.md)
* [Использование Azure CLI 2.0 со службой хранилища Azure](storage-azure-cli.md#create-and-manage-file-shares)
* [Часто задаваемые вопросы](storage-files-faq.md)
* [Устранение неполадок](storage-troubleshoot-file-connection-problems.md)

