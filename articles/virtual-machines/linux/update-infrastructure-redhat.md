---
title: Инфраструктура обновления Red Hat Update Infrastructure | Документация Майкрософт
description: Узнайте об инфраструктуре обновления Red Hat Update Infrastructure для предоставляемых по запросу экземпляров Red Hat Enterprise Linux в Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: borisb
ms.openlocfilehash: b69cc226ca5b4f48747b033e0da5e7f991be112e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915474"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure для предоставляемых по запросу виртуальных машин Red Hat Enterprise Linux в Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) позволяет поставщикам облачных служб (например, Azure) создавать зеркальные копии размещенного с помощью Red Hat содержимого репозитория, создавать пользовательские репозитории с содержимым для Azure и предоставлять пользовательским виртуальным машинам доступ к этому содержимому.

Образы Red Hat Enterprise Linux (RHEL) с оплатой по мере использования (PAYG) предварительно настроены для доступа к Azure RHUI. Никаких дополнительных настроек не требуется. Чтобы получить последние обновления, выполните `sudo yum update`, когда экземпляр RHEL будет готов. Плата за эту службу входит в стоимость программного обеспечения RHEL (PAYG).

## <a name="important-information-about-azure-rhui"></a>Важные сведения об Azure RHUI
* Сейчас Azure RHUI поддерживает только последний вспомогательный выпуск в каждом семействе RHEL (RHEL6 или RHEL7). Чтобы обновить экземпляр виртуальной машины RHEL, подключенный к RHUI, до версии с последним дополнительным номером, выполните команду `sudo yum update`.

    Например, если вы подготовили виртуальную машину на основе образа RHEL 7.2 (PAYG) и выполнили команду `sudo yum update`, то виртуальная машина обновится до версии RHEL 7.4 (последний дополнительный номер версии в семействе RHEL7).

    Чтобы избежать этого, необходимо создать собственный образ, как описано в статье [Подготовка виртуальной машины на основе Red Hat для Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Затем нужно подключить его к другой инфраструктуре обновления ([непосредственно к серверам доставки содержимого Red Hat](https://access.redhat.com/solutions/253273) или [серверу Red Hat Satellite](https://access.redhat.com/products/red-hat-satellite)).

* В стоимость образа RHEL (PAYG) входит плата за доступ к инфраструктуре RHUI, размещенной в Azure. Отмена регистрации виртуальной машины RHEL (PAYG) в инфраструктуре RHUI в Azure не приведет к ее преобразованию в виртуальную машину с использованием собственной лицензии (BYOL). В случае регистрации одной и той же виртуальной машины в другом источнике обновлений вы можете нести двойные _косвенные_ расходы. Первая плата будет взиматься за использование программного обеспечения Azure RHEL. Вторая плата будет взиматься за подписки Red Hat, которые были приобретены ранее. Если требуется использовать другую инфраструктуру обновлений (не инфраструктуру RHUI, размещенную в Azure), рекомендуется создать и развернуть собственные образы (с использованием собственной лицензии). Этот процесс описан в разделе [Подготовка виртуальной машины на основе Red Hat для Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Оба класса образов RHEL (PAYG) в Azure (RHEL for SAP HANA и RHEL for SAP Business Applications) подключаются к выделенным каналам RHUI, которые остаются на определенной версии RHEL с дополнительным номером, что требуется для сертификации SAP. 

* Доступ к размещенной в Azure инфраструктуре RHUI могут получать виртуальные машины с IP-адресами в рамках [диапазонов IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653). Если весь трафик виртуальной машины перенаправляется через локальную сетевую инфраструктуру, может потребоваться настройка определяемых пользователем маршрутов, чтобы виртуальные машины RHEL (PAYG) могли получить доступ к инфраструктуре RHUI в Azure.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP-адреса для серверов доставки содержимого RHUI

Инфраструктура RHUI поддерживается во всех регионах, где доступны предоставляемые по запросу образы RHEL. Сейчас в этот список входят общедоступные регионы, указанные на странице [состояния Azure](https://azure.microsoft.com/status/), регионы, обслуживающие государственные организации США, и регионы Microsoft Azure — Германия. 

При использовании конфигурации сети для усиленного ограничения доступа с виртуальных машин RHEL (PAYG) убедитесь, что для приведенных ниже IP-адресов разрешена операция `yum update`, в зависимости от используемой среды. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>Обновление инфраструктуры RHUI в Azure

В сентябре 2016 года была развернута обновленная инфраструктура RHUI в Azure. В апреле 2017 года работа старой инфраструктуры RHUI в Azure была завершена. Если вы использовали образы RHEL (PAYG) либо их моментальные снимки с сентября 2016 года или более позднего времени, вы автоматически подключились к новой версии инфраструктуры RHUI в Azure. Но если у вас есть более старые моментальные снимки виртуальных машин, их конфигурации необходимо обновить вручную, чтобы обеспечить доступ к инфраструктуре RHUI в Azure, как описано в следующем разделе.

Новые серверы RHUI в Azure развертываются с помощью [диспетчера трафика Azure](https://azure.microsoft.com/services/traffic-manager/). В диспетчере трафика одну конечную точку (rhui-1.microsoft.com) может использовать любая виртуальная машина независимо от региона. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Устранение неполадок подключения к инфраструктуре RHUI в Azure
Если вы столкнулись с проблемами при подключении к инфраструктуре RHUI в Azure с виртуальной машины RHEL (PAYG) в Azure, выполните следующие действия.

1. Проверьте конфигурацию виртуальной машины для конечной точки RHUI в Azure.

    a. Проверьте, содержит ли файл `/etc/yum.repos.d/rh-cloud.repo` ссылку на `rhui-[1-3].microsoft.com` в `baseurl` раздела `[rhui-microsoft-azure-rhel*]`. Если это так, то вы используете новую версию инфраструктуры RHUI Azure.

    Б. Если он указывает на расположение с помощью шаблона `mirrorlist.*cds[1-4].cloudapp.net`, то требуется обновить конфигурацию. Вы используете устаревший моментальный снимок виртуальной машины. Его нужно обновить, чтобы в файле было указано расположение новой версии инфраструктуры RHUI в Azure.

2. Доступ к размещенной в Azure инфраструктуре RHUI могут получать только виртуальные машины с IP-адресами в рамках (диапазонов IP-адресов центра обработки данных Azure) (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Если вы убедились, что IP-адрес подключаемой виртуальной машины находится в диапазоне IP-адресов Azure, но при использовании новой конфигурации вам по-прежнему не удается подключиться к инфраструктуре RHUI в Azure, обратитесь в службу поддержки Майкрософт или Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Процедура обновления вручную для использования серверов Azure RHUI
Эта процедура приводится только для справки. Образы RHEL (PAYG) уже содержат правильную конфигурацию для подключения к инфраструктуре RHUI в Azure. Чтобы вручную обновить конфигурацию для использования серверов RHUI в Azure, выполните следующие действия.

1. Скачайте подпись открытого ключа с помощью curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Проверьте, действителен ли скачанный ключ.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Просмотрите выходные данные, а затем проверьте `keyid` и `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

4. Установите открытый ключ.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Скачайте, проверьте и установить клиент диспетчера пакетов RPM (RPM).
    
    >[!NOTE]
    >Версии пакетов меняются. Если вы вручную подключаетесь к инфраструктуре RHUI в Azure, то последнюю версию пакета клиента для каждого семейства RHEL можно узнать, подготовив последний образ из коллекции.
  
   a. Скачайте компоненты. 
   
    - Для RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Для RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   Б. Выполните проверку.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Проверьте выходные данные, чтобы убедиться, что подпись пакета действительна.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Установите RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

6. По завершении убедитесь, что вы можете получить доступ к инфраструктуре RHUI в Azure с виртуальной машины.

## <a name="next-steps"></a>Дополнительная информация
Сведения о создании виртуальной машины Red Hat Enterprise Linux на основе образа с оплатой по мере использования (PAYG) и о применении размещенной в Azure инфраструктуры RHUI см. на странице [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
