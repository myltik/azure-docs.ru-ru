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
ms.date: 02/13/2017
ms.author: borisb
translationtype: Human Translation
ms.sourcegitcommit: 6b54633b6beed738a93070aa4235ee4e24333b5e
ms.openlocfilehash: e1867aa3c5339373b494744ba26c750bcc11b5b5
ms.lasthandoff: 02/16/2017


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure для предоставляемых по запросу виртуальных машин Red Hat Enterprise Linux в Azure
Виртуальным машинам, которые созданы на основе доступных в Azure Marketplace предоставляемых по запросу образов Red Hat Enterprise Linux (RHEL), обеспечивается доступ к развернутому в Azure решению Red Hat Update Infrastructure (RHUI).  Предоставляемые по запросу экземпляры RHEL имеют доступ к региональному репозиторию yum, благодаря чему они могут получать добавочные обновления.

Список репозитория yum, который находится под управлением RHUI, настраивается в вашем экземпляре RHEL во время подготовки. Поэтому выполнять дополнительную настройку не нужно. Запустите `yum update`, как только экземпляр RHEL будет готов к получению последних обновлений.

> [!NOTE]
> В сентябре 2016 года мы развернули обновленную версию Azure RHUI и в январе 2017 года начали поэтапное завершение работы устаревшей версии Azure RHUI. Если вы использовали образы RHEL (или их моментальные снимки) с сентября 2016 года, то, скорее всего, никаких действий не потребуется. Тем не менее, если у вас есть более старые моментальные снимки или виртуальные машины, их конфигурации необходимо обновить, чтобы обеспечить непрерывный доступ к Azure RHUI.
> 

## <a name="rhui-azure-infrastructure-update"></a>Обновление инфраструктуры Azure RHUI
Начиная с сентября 2016 г. в Azure доступен новый набор серверов Red Hat Update Infrastructure (RHUI). Эти серверы развертываются с помощью [диспетчера трафика Azure](https://azure.microsoft.com/services/traffic-manager/), чтобы конечную точку (rhui-1.micrsoft.com) могла использовать любая виртуальная машина независимо от региона. Новые образы RHEL с оплатой по мере использования (PAYG) в Azure Marketplace (начиная с сентября 2016 г.) указывают на новые серверы Azure RHUI и не требуют каких-либо дополнительных действий.

### <a name="determine-if-action-is-required"></a>Как определить, требуется ли действие
Если вы столкнулись с проблемами при подключении к Azure RHUI с виртуальной машины RHEL (PAYG) в Azure, выполните следующие действия.
1. Проверка конфигурации виртуальной машины для конечной точки Azure RHUI

    Проверьте, содержит ли файл `/etc/yum.repos.d/rh-cloud.repo` ссылку на `rhui-[1-3].microsoft.com` в baseurl раздела `[rhui-microsoft-azure-rhel*]`. Если это так, то вы используете новую версию Azure RHUI.

    Если он указывает на местоположение с помощью шаблона `mirrorlist.*cds[1-4].cloudapp.net`, то требуется обновить конфигурацию.

    Если при использовании новой конфигурации по-прежнему не удается подключиться к Azure RHUI, зарегистрируйте обращение в службу поддержки Майкрософт или Red Hat.

    > [!NOTE]
    > Доступ к размещенной в Azure инфраструктуре RHUI могут получать виртуальные машины с IP-адресами в рамках [диапазонов IP-адресов центра обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
    > 

2. Если при проверке по-прежнему доступна устаревшая версия Azure RHUI и вы хотите автоматически обновить конфигурацию, выполните следующую команду.

    Укажите `sudo yum update RHEL6` или `sudo yum update RHEL7` в зависимости от версии семейства RHEL.

3. Если вы больше не можете подключиться к устаревшей версии Azure RHUI, выполните вручную действия, описанные в следующем разделе.

4. Обязательно обновите конфигурацию исходного образа или моментального снимка, с помощью которого была подготовлена виртуальная машина.

### <a name="phased-shutdown-of-the-old-azure-rhui"></a>Поэтапное завершение работы устаревшей версии Azure RHUI
При завершении работы устаревшей версии Azure RHUI мы ограничиваем к ней доступ следующим образом.

1. Еще больше ограничивается доступ (ACL) и задаются IP-адреса, которые уже подключается к инфраструктуре. Возможные побочные эффекты: если продолжать использовать устаревшую версию Azure RHUI, то новые виртуальные машины не смогут подключаться к ней. Виртуальные машины RHEL с динамическими IP-адресами, проходящие последовательность завершения работы, освобождения и запуска, могут получить новый IP-адрес и поэтому перестать подключаться к устаревшей версии Azure RHUI.

2. Завершение работы зеркальных серверов доставки содержимого. Возможные побочные эффекты: по мере завершения работы все большего числа серверов доставки содержимого может увеличиваться время обслуживания `yum update`, время ожидания может все чаще истекать, а затем вы вообще утратите возможность подключаться к устаревшей версии Azure RHUI.

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>IP-адреса для новых серверов RHUI доставки содержимого
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

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Процедура обновления вручную для использования новых серверов Azure RHUI
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

Загрузите, проверьте и установите RPM клиента

Загрузка для RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Для RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
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

По завершении убедитесь, что вы можете получить доступ к Azure RHUI с виртуальной машины

### <a name="all-in-one-script-for-automating-the-preceding-task"></a>Сценарий "все в одном" для автоматизации предыдущей задачи
Используйте приведенный далее сценарий для автоматизации задачи обновления необходимых виртуальных машин до новых серверов Azure RHUI.

```sh
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Обзор RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) является высокомасштабируемым решением для управления содержимым репозитория yum для облачных экземпляров Red Hat Enterprise Linux, размещенных сертифицированными компанией Red Hat поставщиками облачных служб. Являясь продуктом на основе вышестоящего проекта Pulp, RHUI позволяет поставщикам облачных решений локально зеркалировать размещенное Red Hat содержимое репозитория, создавать пользовательские репозитории с собственным содержимым и предоставлять большим группам пользователей доступ к таким репозиториям с помощью системы доставки содержимого с балансировкой нагрузки.

## <a name="regions-where-rhui-is-available"></a>Регионы, в которых доступна инфраструктура RHUI
Инфраструктура RHUI поддерживается во всех регионах, где доступны предоставляемые по запросу образы RHEL. В настоящее время в этот список входят общедоступные регионы, указанные на странице [мониторинга состояния Azure](https://azure.microsoft.com/status/), регионы, обслуживающие государственные организации США, и регионы Azure для Германии. Доступ к RHUI для виртуальных машин, развернутых из предоставляемых по запросу образов RHEL, входит в стоимость образа. Так как в будущем планируется расширить регионы, в которых будут доступны предоставляемые по запросу экземпляры RHEL, сведения об их доступности по регионам и странам будут обновлены.

> [!NOTE]
> Доступ к размещенной в Azure инфраструктуре RHUI могут получать виртуальные машины с IP-адресами в рамках [диапазонов IP-адресов центра обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

## <a name="get-updates-from-another-update-repository"></a>Получение обновлений из другого репозитория обновлений
Если вам нужно получить обновления из другого репозитория обновлений (вместо RHUI в Azure), сначала необходимо отменить регистрацию своих экземпляров в RHUI. Затем необходимо зарегистрировать их в требуемой инфраструктуре обновления (например, Red Hat Satellite или CDN Red Hat Customer Portal). Кроме того, необходима подписка Red Hat для этих служб и регистрация [Red Hat Cloud Access в Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Чтобы отменить регистрацию RHUI и зарегистрировать экземпляр в другой инфраструктуре обновления, сделайте следующее.

1. Измените все `enabled=1` на `enabled=0` в репозитории /etc/yum.repos.d/rh-cloud.repo. Например:
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. Измените значение `enabled=0` на `enabled=1` в файле /etc/yum/pluginconf.d/rhnplugin.conf.
3. Затем зарегистрируйтесь в нужной инфраструктуре, например на портале клиента Red Hat. Дополнительные сведения о регистрации системы на портале клиента Red Hat и оформлении подписки см. в [руководстве по решению Red Hat](https://access.redhat.com/solutions/253273).

> [!NOTE]
> В стоимость образа RHEL с оплатой по мере использования (PAYG) входит плата за доступ к инфраструктуре RHUI, размещенной в Azure. Отмена регистрации виртуальной машины RHEL (PAYG) в инфраструктуре RHUI в Azure не преобразовывает ее в виртуальную машину с использованием собственной лицензии (BYOL). Регистрация одной и той же виртуальной машины в другом источнике обновлений может привести к двойной оплате: сначала вы оплатите программное обеспечение Azure RHEL, затем — подписки Red Hat. 
> 
> Если требуется использовать другую инфраструктуру обновлений (не RHUI, размещенную в Azure), рекомендуется создать и развернуть собственный образ (с использованием собственной лицензии), как описано в статье [Подготовка виртуальной машины на основе Red Hat для Azure](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании виртуальной машины Red Hat Enterprise Linux из образа с оплатой по мере использования и о применении размещенной в Azure RHUI см. на странице [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). В существующем экземпляре RHEL можно использовать `yum update` без дополнительной настройки.


