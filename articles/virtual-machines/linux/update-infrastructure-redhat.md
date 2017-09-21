---
title: "Инфраструктура обновления Red Hat Update Infrastructure | Документация Майкрософт"
description: "Узнайте об инфраструктуре обновления Red Hat Update Infrastructure для предоставляемых по запросу экземпляров Red Hat Enterprise Linux по запросу в Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 43d9095bcbd490abc6d01c214a17b8586f92ed1e
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure для предоставляемых по запросу виртуальных машин Red Hat Enterprise Linux в Azure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) позволяет поставщикам облачных служб (например, Azure) создавать зеркальные копии размещенного с помощью Red Hat содержимого репозитория, создавать пользовательские репозитории с содержимым для Azure и предоставлять пользовательским виртуальным машинам доступ к этому содержимому.

Образы Red Hat Enterprise Linux (RHEL) с оплатой по мере использования (PAYG) предварительно настроены для доступа к Azure RHUI. Поэтому выполнять дополнительную настройку не нужно. Запустите `sudo yum update`, как только экземпляр RHEL будет готов к получению последних обновлений. Плата за эту службу входит в стоимость программного обеспечения RHEL (PAYG).

## <a name="important-information-about-azure-rhui"></a>Важные сведения об Azure RHUI

1. Сейчас RHUI поддерживает только последний вспомогательный выпуск этого семейства RHEL (RHEL6 или RHEL7). Экземпляр виртуальной машины RHEL, подключенный к RHUI, обновится до версии с последним дополнительным номером при выполнении команды `sudo yum update`. 

    Например, если вы подготовили виртуальную машину из образа RHEL 7.2 (PAYG) и выполнили команду `sudo yum update`, виртуальная машина обновится до версии RHEL 7.4 (текущая актуальная версия с дополнительным номером семейства RHEL7).

    Чтобы избежать этого, необходимо создать собственный образ, как описано в статье [Подготовка виртуальной машины на основе Red Hat для Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), и подключить его к другой инфраструктуре обновления ([непосредственно к серверам доставки содержимого Red Hat](https://access.redhat.com/solutions/253273) или к серверу Red Hat Satellite).

2. В стоимость образа RHEL (PAYG) входит плата за доступ к инфраструктуре RHUI, размещенной в Azure. Отмена регистрации виртуальной машины RHEL (PAYG) в инфраструктуре RHUI в Azure не преобразовывает ее в виртуальную машину с использованием собственной лицензии (BYOL). Регистрация одной и той же виртуальной машины в другом источнике обновлений может привести к _непрямой_ двойной оплате: сначала вы оплатите программное обеспечение Azure RHEL, а затем — подписки Red Hat, приобретенные ранее. Если требуется использовать другую инфраструктуру обновлений (не инфраструктуру RHUI, размещенную в Azure), рекомендуется создать и развернуть собственный образ (с использованием собственной лицензии), как описано в статье [Подготовка виртуальной машины на основе Red Hat для Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

3. Оба класса образов RHEL (PAYG) в Azure (RHEL for SAP HANA и RHEL for SAP Business Applications) подключаются к выделенным каналам RHUI, которые остаются на определенной версии RHEL с дополнительным номером, что требуется для сертификации SAP. 

4. Доступ к размещенной в Azure инфраструктуре RHUI могут получать виртуальные машины с IP-адресами в рамках [диапазонов IP-адресов центра обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Если весь трафик виртуальной машины перенаправляется через локальную сетевую инфраструктуру, может потребоваться настройка определяемых пользователем маршрутов, чтобы виртуальные машины RHEL (PAYG) могли получить доступ к Azure RHUI.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>IP-адреса для серверов доставки содержимого RHUI
Инфраструктура RHUI поддерживается во всех регионах, где доступны предоставляемые по запросу образы RHEL. Сейчас в этот список входят общедоступные регионы, указанные на странице [состояния Azure](https://azure.microsoft.com/status/), регионы, обслуживающие государственные организации США, и регионы Azure для Германии. 

При использовании конфигурации сети для усиленного ограничения доступа с виртуальных машин RHEL (PAYG) убедитесь, что для приведенных ниже IP-адресов разрешена операция `yum update`, в зависимости от используемой среды. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>Обновление инфраструктуры Azure RHUI

В сентябре 2016 года мы развернули обновленную версию Azure RHUI, а в апреле 2017 года завершили работу устаревшей версии Azure RHUI. Если вы использовали образы RHEL (PAYG) либо их моментальные снимки с сентября 2016 года или более позднего времени, вы автоматически подключились к новой версии Azure RHUI. Но если у вас есть более старые моментальные снимки или виртуальные машины, их конфигурации необходимо обновить вручную, чтобы обеспечить доступ к Azure RHUI, как описано ниже.

Новые серверы Azure RHUI развертываются с помощью [диспетчера трафика Azure](https://azure.microsoft.com/services/traffic-manager/), чтобы одну и ту же конечную точку (rhui-1.microsoft.com) могли использовать все виртуальные машины независимо от региона. 

### <a name="troubleshooting-connection-problems-to-azure-rhui"></a>Устранение неполадок с подключением к Azure RHUI
Если вы столкнулись с проблемами при подключении к Azure RHUI c виртуальной машины RHEL (PAYG) в Azure, выполните следующие действия.
1. Проверка конфигурации виртуальной машины для конечной точки Azure RHUI

    Проверьте, содержит ли файл `/etc/yum.repos.d/rh-cloud.repo` ссылку на `rhui-[1-3].microsoft.com` в baseurl раздела `[rhui-microsoft-azure-rhel*]`. Если это так, то вы используете новую версию Azure RHUI.

    Если он указывает на местоположение с помощью шаблона `mirrorlist.*cds[1-4].cloudapp.net`, то требуется обновить конфигурацию. Вы используете устаревший моментальный снимок виртуальной машины. Обновите его, чтобы в файле указывалось местоположение новой версии Azure RHUI.

2. Доступ к размещенной в Azure инфраструктуре RHUI могут получать только виртуальные машины с IP-адресами в рамках [диапазонов IP-адресов центра обработки данных Microsoft Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Если вы убедились, что IP-адрес подключаемой виртуальной машины находится в диапазоне IP-адресов Azure, но при использовании новой конфигурации вам по-прежнему не удается подключиться к Azure RHUI, обратитесь в службу поддержки Майкрософт или Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Процедура обновления вручную для использования серверов Azure RHUI
Эта процедура приводится только для справки. В образах RHEL (PAYG) уже задана правильная конфигурация для подключения к Azure RHUI.

Загрузите подпись открытого ключа (с помощью curl)

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Проверьте загруженный ключ

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Проверьте выходные данные, `keyid` и `user ID packet`:

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

Установите открытый ключ

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Скачайте, проверьте и установите RPM клиент. 

> [!NOTE]
> Версии пакетов меняются. Если вы вручную подключаетесь к Azure RHUI, последнюю версию пакета клиента для каждого семейства RHEL можно узнать, подготовив последний образ из коллекции.
> 

Загрузка для RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
```

Для RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
```

Проверка.

```bash
rpm -Kv azureclient.rpm
```

Проверьте подпись пакета в выходных данных

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Установите RPM

```bash
sudo rpm -U azureclient.rpm
```

По завершении убедитесь, что вы можете получить доступ к Azure RHUI с виртуальной машины.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании виртуальной машины Red Hat Enterprise Linux из образа с оплатой по мере использования и о применении размещенной в Azure RHUI см. на странице [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 
