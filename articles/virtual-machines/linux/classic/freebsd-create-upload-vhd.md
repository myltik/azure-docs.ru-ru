---
title: "Создание и передача образа виртуальной машины FreeBSD | Документация Майкрософт"
description: "Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл), содержащий операционную систему FreeBSD, для создания виртуальной машины Azure."
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 71ba93c64657725b48ad5915c6bb26dc32e5434d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Создание и отправка виртуального жесткого диска FreeBSD в Azure
В этой статье описывается, как создать и передать виртуальный жесткий диск, содержащий операционную систему FreeBSD. После передачи его можно использовать как свой собственный образ для создания виртуальной машины в Azure.

> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Дополнительные сведения о передаче виртуального жесткого диска с помощью модели Resource Manager см. [здесь](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Технические условия
В данной статье предполагается, что у вас есть следующие элементы:

* **Подписка Azure**. Если у вас нет учетной записи, то ее можно создать, что займет всего лишь несколько минут. Если у вас есть подписка MSDN, см. страницу [Ежемесячная сумма денег на счете в Azure для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). В противном случае узнайте, как [создать бесплатную пробную учетную запись](https://azure.microsoft.com/pricing/free-trial/).  
* **Инструменты Azure PowerShell**. Модуль Azure PowerShell необходимо установить и настроить на использование вашей подписки Azure. Сведения о скачивании модуля см. в разделе [загрузок Azure](https://azure.microsoft.com/downloads/). Руководство по установке и настройке этого модуля см. здесь. Для передачи VHD-файла используйте командлет из раздела [загрузок Azure](https://azure.microsoft.com/downloads/).
* **Операционная система FreeBSD, установленная в VHD-файл**. На виртуальный жесткий диск необходимо установить поддерживаемую операционную систему FreeBSD. Существует несколько средств для создания VHD-файлов. Например, для создания VHD-файла и установки операционной системы можно использовать решение для виртуализации, например Hyper-V. Инструкции по установке и использованию Hyper-V см. в статье [Установка Hyper-V и создание виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. Вы можете преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Кроме того, [на сайте MSDN доступно руководство по использованию FreeBSD с Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Эта задача включает в себя следующие четыре шага:

## <a name="step-1-prepare-the-image-for-upload"></a>Шаг 1. Подготовка образа для передачи
Из виртуальной машины, где была установлена операционная система FreeBSD, сделайте следующее:

1. Включите DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Включите SSH.

    Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Он включается по умолчанию после установки с диска FreeBSD. 
3. Настройте последовательную консоль.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Установите sudo.

    Учетная запись root в Azure отключена. Поэтому вам необходимо использовать sudo от лица непривилегированного пользователя для запуска команд с повышенными привилегиями.

        # pkg install sudo
   
5. Необходимые условия для агента Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Установите агент Azure.

    Последний выпуск агента Azure всегда можно найти на сайте [github](https://github.com/Azure/WALinuxAgent/releases). Версия 2.0.10 и более поздние версии официально поддерживают FreeBSD 10 и 10.1, а версия 2.1.4 и более поздние (включая 2.2.x) официально поддерживает FreeBSD 10.2 и более поздние версии.

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
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Отзовите систему.

    Отзовите систему, чтобы очистить ее и сделать пригодной для повторной подготовки. Приведенная ниже команда также удаляет последнюю подготовленную учетную запись пользователя и связанные с ней данные.

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Теперь вы можете завершить работу виртуальной машины.

## <a name="step-2-prepare-the-connection-to-azure"></a>Шаг 2. Подготовка подключения к Azure
Убедитесь, что вы используете интерфейс командной строки Azure в классической модели развертывания (`azure config mode asm`), а затем войдите со своей учетной записью.

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>Шаг 3. Передача VHD-файла

Для передачи VHD-файла нужна учетная запись хранения. Можно выбрать существующую учетную запись хранения или [создать новую](../../../storage/common/storage-create-storage-account.md).

При передаче VHD-файла его можно поместить в любом месте внутри хранилища BLOB-объектов. Ниже приведены некоторые термины, которые будут использоваться при отправке файла.

* **BlobStorageURL** — URL-адрес для учетной записи хранения, созданной на шаге 2.
* **YourImagesFolder** — контейнер внутри хранилища BLOB-объектов, где будут храниться образы.
* **VHDName** — метка, которая отображается на портале Azure для определения виртуального жесткого диска.
* **PathToVHDFile** — это полный путь и имя файла .vhd.

В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>Шаг 4. Создание виртуальной машины с использованием переданного VHD-файла
После передачи VHD-файл можно добавить в качестве образа в список пользовательских образов, связанных с вашей подпиской, и создать виртуальную машину с помощью этого пользовательского образа.

1. В окне Azure PowerShell, использованном при выполнении предыдущего шага, введите:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > В качестве типа ОС используйте Linux. Текущая версия Azure PowerShell принимает в качестве значения параметра только Linux или Windows.
   >
   >
2. После выполнения предыдущих шагов новый образ отображается при выборе вкладки **Образы** на классическом портале Azure.  

    ![Выбор образа](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Создайте виртуальную машину из коллекции. Этот новый образ теперь доступен в разделе **Мои образы**.
4. Выберите новый образ. Затем выполните указания для настройки имени узла, пароля, SSH-ключа и т. п.

    ![Пользовательский образ](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. После завершения подготовки вы увидите в Azure запущенную виртуальную машину FreeBSD.

    ![Образ FreeBSD в Azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
