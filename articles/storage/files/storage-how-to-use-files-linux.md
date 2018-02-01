---
title: "Использование файлов Azure в Linux | Документы Майкрософт"
description: "Узнайте, как подключить файловый ресурс Azure через протокол SMB в Linux."
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
ms.date: 12/20/2017
ms.author: renash
ms.openlocfilehash: cca0d315a815faca5db07099b8e8e451ef55fad5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="use-azure-files-with-linux"></a>Использование файлов Azure в Linux
[Файлы Azure](storage-files-introduction.md) — это простая в использовании облачная файловая система от Майкрософт. Файловые ресурсы Azure можно подключить в дистрибутивах Linux с помощью [CIFS-клиента в ядре](https://wiki.samba.org/index.php/LinuxCIFS). В этой статье описаны два способа подключения файлового ресурса Azure: по запросу с помощью команды `mount` и при загрузке путем создания записи в `/etc/fstab`.

> [!NOTE]  
> Чтобы подключить файловый ресурс Azure за пределами региона Azure, в котором она размещается, например локально или в другом регионе Azure, операционная система должна поддерживать функции шифрования SMB 3.0.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Предварительные требования для подключения файлового ресурса Azure с помощью Linux и пакета cifs-utils
* **Выберите дистрибутив Linux с возможностью установки пакета cifs-utils.**  
    В коллекции Azure доступны следующие дистрибутивы Linux:

    * Ubuntu Server 14.04 или более поздней версии;
    * RHEL 7 или более поздней версии;
    * CentOS 7 или более поздней версии;
    * Debian 8 или более поздней версии;
    * openSUSE 13.2 или более поздней версии.
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**Установите пакет cifs-utils.**  
    Пакет cifs-utils можно установить с помощью диспетчера пакетов в дистрибутиве Linux по своему усмотрению. 

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

* <a id="smb-client-reqs"></a>**Поймите требования к клиенту SMB.**  
    Службу файлов Azure можно подключить с помощью SMB 2.1 или SMB 3.0. Для подключений, поступающих от клиентов в локальной среде или в других регионах Azure, служба файлов Azure отклонит SMB 2.1 (или SMB 3.0 без шифрования). Если для учетной записи хранения включен параметр *Требуется безопасное перемещение*, то служба файлов Azure разрешит только подключения по протоколу SMB 3.0 с шифрованием.
    
    Поддержка шифрования SMB 3.0 появилась в версии ядра Linux 4.11 и была добавлена в предшествующие версии ядер для популярных дистрибутивов Linux. На момент публикации этого документа данную функцию поддерживают следующие дистрибутивы из коллекции Azure:

    - Ubuntu Server 16.04 или более поздней версии;
    - openSUSE 42.3 или более поздней версии;
    - SUSE Linux Enterprise Server 12 с пакетом обновления 3 (SP3) или более поздней версии.
    
    Если ваш дистрибутив Linux отсутствует в списке, можно проверить версию ядра Linux с помощью следующей команды.

    ```
    uname -r
    ```

* **Выберите разрешения для каталога и файлов подключаемого общедоступного ресурса**. В приведенных ниже примерах используется разрешение `0777`, чтобы все пользователи имели разрешения на чтение, запись и выполнение файлов. При необходимости можно заменить его другим [разрешением chmod](https://en.wikipedia.org/wiki/Chmod). 

* **Имя учетной записи хранения**: чтобы подключить файловый ресурс Azure, требуется имя учетной записи хранения.

* **Ключ учетной записи хранения**: чтобы подключить файловый ресурс Azure, требуется первичный (или вторичный) ключ к хранилищу данных. В настоящее время ключи SAS для подключения не поддерживаются.

* **Откройте порт 445**. Взаимодействие SMB выполняется через TCP-порт 445. Проверьте, чтобы брандмауэр не блокировал TCP-порты 445 с клиентского компьютера.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Подключение файлового ресурса Azure по запросу с помощью `mount`
1. **[Установите пакет cifs-utils для вашего дистрибутива Linux](#install-cifs-utils)**.

2. **Создайте папку для точки подключения**. Папку для точки подключения можно создать в любом месте файловой системы, но общепринято создавать ее в папке `/mnt`. Например: 

    ```
    mkdir /mnt/MyAzureFileShare
    ```

3. **Используйте команду mount для подключения файлового ресурса Azure**. Не забудьте заменить значения `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` и `<mount-point>` соответствующими значениями для своей среды. Если ваш дистрибутив Linux поддерживает SMB 3.0 с шифрованием (дополнительные сведения см. в разделе [Использование файлов Azure в Linux](#smb-client-reqs)), укажите `3.0` для `<smb-version>`. Для дистрибутивов Linux, которые не поддерживают SMB 3.0 с шифрованием, укажите `2.1` для `<smb-version>`. Обратите внимание на то, что с помощью SMB 3.0 файловый ресурс Azure может быть подключен только за пределами региона Azure (включая локальную среду или другой регион Azure). 

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Завершив работу с файловым ресурсом Azure, вы можете отключить его, выполнив команду `sudo umount <mount-point>`.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Создание постоянной точки подключения для файлового ресурса Azure с помощью `/etc/fstab`
1. **[Установите пакет cifs-utils для вашего дистрибутива Linux](#install-cifs-utils)**.

2. **Создайте папку для точки подключения**. Папку для точки подключения можно создать в любом месте файловой системы, но общепринято создавать ее в папке `/mnt`. Где бы вы ни создали ее, запишите абсолютный путь к этой папке. Например, следующая команда создает новую папку в папке `/mnt` (путь является абсолютным).

    ```
    sudo mkdir /mnt/MyAzureFileShare
    ```

3. **Используйте следующую команду, чтобы добавить следующую строку в `/etc/fstab`**. Не забудьте заменить значения `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` и `<mount-point>` соответствующими значениями для своей среды. Если ваш дистрибутив Linux поддерживает SMB 3.0 с шифрованием (дополнительные сведения см. в разделе [Использование файлов Azure в Linux](#smb-client-reqs)), укажите `3.0` для `<smb-version>`. Для дистрибутивов Linux, которые не поддерживают SMB 3.0 с шифрованием, укажите `2.1` для `<smb-version>`. Обратите внимание на то, что с помощью SMB 3.0 файловый ресурс Azure может быть подключен только за пределами региона Azure (включая локальную среду или другой регион Azure). 

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Вместо перезагрузки можно использовать `sudo mount -a`, чтобы подключить файловый ресурс после изменения `/etc/fstab`.

## <a name="feedback"></a>Отзыв
Пользователи Linux, нам очень интересно ваше мнение!

Файлы Azure для группы пользователей Linux включают форум, на котором можно поделиться своим мнением и опытом адаптации хранилища файлов в Linux. Чтобы вступить в группу пользователей, отправьте электронное письмо в группу [Пользователи файлов Azure в Linux](mailto:azurefileslinuxusers@microsoft.com).

## <a name="next-steps"></a>Дополнительная информация
Дополнительную информацию о службе файлов Azure см. по следующим ссылкам.
* [Общие сведения о службе файлов Azure](storage-files-introduction.md)
* [Planning for an Azure Files deployment](storage-files-planning.md) (Планирование развертывания службы файлов Azure)
* [Часто задаваемые вопросы](../storage-files-faq.md)
* [Устранение неполадок](storage-troubleshoot-linux-file-connection-problems.md)