---
title: "Создание и передача образа виртуальной машины FreeBSD | Документация Майкрософт"
description: "Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл), содержащий операционную систему FreeBSD, для создания виртуальной машины Azure."
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: d988b30457e1882a90ce6aac4efaf87a0d71c858


---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Создание и отправка виртуального жесткого диска FreeBSD в Azure
В этой статье описывается, как создать и передать виртуальный жесткий диск, содержащий операционную систему FreeBSD. После передачи его можно использовать как свой собственный образ для создания виртуальной машины в Azure.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Дополнительные сведения о передаче виртуального жесткого диска с помощью модели Resource Manager см. [здесь](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Предварительные требования
В данной статье предполагается, что у вас есть следующие элементы:

* **Подписка Azure**. Если у вас нет учетной записи, то ее можно создать, что займет всего лишь несколько минут. Если у вас есть подписка MSDN, см. страницу [Ежемесячная сумма денег на счете в Azure для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). В противном случае узнайте, как [создать бесплатную пробную учетную запись](https://azure.microsoft.com/pricing/free-trial/).  
* **Инструменты Azure PowerShell**. Модуль Azure PowerShell необходимо установить и настроить на использование вашей подписки Azure. Сведения о скачивании модуля см. в разделе [загрузок Azure](https://azure.microsoft.com/downloads/). Руководство по установке и настройке этого модуля см. здесь. Для передачи VHD-файла используйте командлет из раздела [загрузок Azure](https://azure.microsoft.com/downloads/).
* **Операционная система FreeBSD, установленная в VHD-файл**. На виртуальный жесткий диск необходимо установить поддерживаемую операционную систему FreeBSD. Существует несколько средств для создания VHD-файлов. Например, для создания VHD-файла и установки операционной системы можно использовать решение для виртуализации, например Hyper-V. Инструкции по установке и использованию Hyper-V см. в статье [Установка Hyper-V и создание виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. Вы можете преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, [на сайте MSDN доступно руководство по использованию FreeBSD с Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Эта задача включает в себя следующие пять шагов.

## <a name="step-1-prepare-the-image-for-upload"></a>Шаг 1. Подготовка образа для передачи
Из виртуальной машины, где была установлена операционная система FreeBSD, сделайте следующее:

1. Включите DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Включите SSH.

    SSH включается по умолчанию после установки с диска. Если по какой-либо причине он не включен или вы используете непосредственно виртуальный жесткий диск FreeBSD, введите следующую команду:

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart
3. Настройте последовательную консоль.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Установите sudo.

    Учетная запись root в Azure отключена. Поэтому вам необходимо использовать sudo от лица непривилегированного пользователя для запуска команд с повышенными привилегиями.

        # pkg install sudo
   ;
5. Необходимые условия для агента Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Установите агент Azure.

    Последний выпуск агента Azure всегда можно найти на сайте [github](https://github.com/Azure/WALinuxAgent/releases). Версия 2.0.10 и более поздние версии официально поддерживают FreeBSD 10 и 10.1, а версия 2.1.4 официально поддерживает FreeBSD 10.2 и более поздние версии.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    В качестве примера версии 2.0 будем использовать версию 2.0.16.

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    В качестве примера версии 2.1 будем использовать версию 2.1.4.

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > После установки агента Azure рекомендуется проверить его работу.
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log
7. Отзовите систему.

    Отзовите систему, чтобы очистить ее и сделать пригодной для повторной подготовки. Приведенная ниже команда также удаляет последнюю подготовленную учетную запись пользователя и связанные с ней данные.

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Теперь вы можете завершить работу виртуальной машины.

## <a name="step-2-create-a-storage-account-in-azure"></a>Шаг 2. Создание учетной записи хранения в Azure
Чтобы передать VHD-файл, который можно использовать в Azure для создания виртуальной машины, потребуется учетная запись хранения. Для создания учетной записи хранения можно использовать классический портал Azure.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com).
2. На панели команд нажмите **New**(Создать).
3. Последовательно выберите **Службы данных** > **Хранилище** > **Быстрое создание**.

    ![Быстрое создание учетной записи хранения](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)
4. Заполните поля, как показано ниже:

   * В поле **URL-адрес** введите имя поддомена, которое нужно использовать в URL-адресе для учетной записи хранения. Записи могут содержать от 3 до 24 строчных букв и цифр. Это имя становится именем узла в URL-адресе, используемом для адресации ресурсов хранилища BLOB-объектов, хранилища очередей или хранилища таблиц Azure для подписки.
   * В раскрывающемся меню **Расположение или территориальная группа** выберите **расположение или территориальную группу** для этой учетной записи хранения. Указав территориальную группу, можно разместить облачные службы и хранилища в одном центре обработки данных.
   * В поле **Репликация** укажите, следует ли использовать **геоизбыточную** репликацию для учетной записи хранения. По умолчанию георепликация включена. Этот параметр позволяет бесплатно выполнять репликацию данных в дополнительное расположение. Таким образом хранилище переключится на это расположение в случае аварийного отказа основного расположения. Дополнительное местоположение назначается автоматически и не может быть изменено. Если в соответствии с законодательными требованиями или организационной политикой требуется более жесткий контроль расположения облачного хранилища, георепликацию можно отключить. Однако следует помнить, что если вы опять включите георепликацию, с вас будет взята однократная плата за репликацию существующих данных в дополнительное местоположение. Службы хранения без георепликации предлагаются со скидкой. Дополнительные сведения об управлении георепликацией учетных записей хранения см. в статье [Репликация службы хранилища Azure](../storage/storage-redundancy.md).

     ![Введите сведения об учетной записи хранения](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)
5. Щелкните **Создать учетную запись хранения**. Учетная запись появится в списке раздела **Хранилище**.

    ![Учетная запись хранения успешно создана](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)
6. Теперь создайте контейнер для переданных VHD-файлов. Введите имя учетной записи хранения и выберите **Контейнеры**.

    ![Информация об учетной записи хранения](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)
7. Щелкните **Создать контейнер**.

    ![Информация об учетной записи хранения](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)
8. В поле **Имя** введите имя своего контейнера. Затем в раскрывающемся меню **Доступ** выберите требуемый тип политики доступа.

    ![Имя контейнера](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > По умолчанию доступ к контейнеру предоставляется только владельцу учетной записи. Чтобы разрешить общий доступ на чтение для больших двоичных объектов в контейнере, но не к свойствам и метаданным контейнера, используйте параметр **Общедоступный BLOB-объект** . Чтобы разрешить полный общий доступ на чтение для контейнера и больших двоичных объектов, используйте параметр **Общедоступный контейнер** .
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>Шаг 3. Подготовка подключения к Azure
Перед передачей VHD-файла необходимо установить безопасное соединение между компьютером и вашей подпиской Azure. Это можно сделать, используя метод Azure Active Directory (Azure AD) или метод сертификатов.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Использование метода Azure AD для передачи VHD-файла
1. Откройте консоль Azure PowerShell.
2. Введите следующую команду:   
    `Add-AzureAccount`

    После выполнения команды откроется окно входа и вы сможете войти, используя свою рабочую или учебную учетную запись.

    ![Окно PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure выполняет проверку подлинности и сохраняет учетные данные, а затем закрывает окно.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Использование метода сертификата для передачи VHD-файла
1. Откройте консоль Azure PowerShell.
2. Введите  `Get-AzurePublishSettingsFile`.
3. В открывшемся окне браузера появится запрос на скачивание PUBLISHSETTINGS-файла. Этот файл содержит сведения и сертификат для подписки Azure.

    ![Страница скачивания браузера](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. Сохраните файл .publishsettings.
5. Тип:  `Import-AzurePublishSettingsFile <PathToFile>`, где `<PathToFile>` — это полный путь к PUBLISHSETTINGS-файлу.

   Дополнительные сведения см. в статье [Get started with Azure cmdlets](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) (Начало работы с командлетами Azure).

   Дополнительные сведения об установке и настройке PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-upload-the-vhd-file"></a>Шаг 4. Загрузка файла VHD
При передаче VHD-файла его можно поместить в любом месте внутри хранилища BLOB-объектов. Ниже приведены некоторые термины, которые будут использоваться при передаче файла.

* **BlobStorageURL** — URL-адрес для учетной записи хранения, созданной на шаге 2.
* **YourImagesFolder** — контейнер внутри хранилища BLOB-объектов, где будут храниться образы.
* **VHDName** — это метка, которая отображается на классическом портале Azure для идентификации виртуального жесткого диска.
* **PathToVHDFile** — это полный путь и имя файла .vhd.

В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Шаг 5. Создание виртуальной машины с использованием переданного VHD-файла
После передачи VHD-файл можно добавить в качестве образа в список пользовательских образов, связанных с вашей подпиской, и создать виртуальную машину с помощью этого пользовательского образа.

1. В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > В качестве типа ОС используйте Linux. Текущая версия Azure PowerShell принимает в качестве значения параметра только Linux или Windows.
   >
   >
2. После выполнения предыдущих шагов новый образ отображается при выборе вкладки **Образы** на классическом портале Azure.  

    ![Выбор образа](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)
3. Создайте виртуальную машину из коллекции. Этот новый образ теперь доступен в разделе **Мои образы**.
4. Выберите новый образ. Затем выполните указания для настройки имени узла, пароля, SSH-ключа и т. п.

    ![Пользовательский образ](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. После завершения подготовки вы увидите в Azure запущенную виртуальную машину FreeBSD.

    ![Образ FreeBSD в Azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)



<!--HONumber=Nov16_HO3-->


