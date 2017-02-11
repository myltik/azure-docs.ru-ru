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
ms.date: 10/14/2016
ms.author: borisb
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 8b00d3c4461494d35665c976e0e1098a246abb68


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat Update Infrastructure для предоставляемых по запросу виртуальных машин Red Hat Enterprise Linux в Azure
Виртуальным машинам, которые созданы на основе доступных в Azure Marketplace предоставляемых по запросу образов Red Hat Enterprise Linux (RHEL), обеспечивается доступ к развернутому в Azure решению Red Hat Update Infrastructure (RHUI).  Предоставляемые по запросу экземпляры RHEL имеют доступ к региональному репозиторию yum, благодаря чему они могут получать добавочные обновления.

Список репозитория yum, который находится под управлением RHUI, настраивается в вашем экземпляре RHEL во время подготовки. Поэтому выполнять дополнительную настройку не нужно. Запустите `yum update`, как только экземпляр RHEL будет готов к получению последних обновлений.

> [!NOTE]
> Инфраструктура Azure RHUI недавно была обновлена (сентябрь 2016 г.) и требует изменения конфигурации существующих экземпляров RHEL для обеспечения непрерывного доступа к Azure RHUI. Подробные сведения см. в разделе, посвященному обновлению инфраструктуры Azure RHUI.
> 
> 

## <a name="rhui-azure-infrastructure-update"></a>Обновление инфраструктуры Azure RHUI
Начиная с сентября 2016 г. в Azure доступен новый набор серверов Red Hat Update Infrastructure (RHUI). Эти серверы развертываются с помощью [диспетчера трафика Azure](https://azure.microsoft.com/services/traffic-manager/), чтобы конечную точку (rhui-1.micrsoft.com) могла использовать любая виртуальная машина независимо от региона. На этих серверах используется сертификат SSL, связанный с хорошо известным центром сертификации (Baltimore root). Автоматизация этого обновления может быть опасной для клиентов, использующих списки управления доступом или настраиваемые таблицы маршрутизации для серверов обновления RHUI, поэтому данное обновление выполняется с согласия пользователя. Эта страница содержит инструкции по подключению новых серверов и полный сценарий использования автоматического режима (после проверки отдельных шагов). Новые образы RHEL PAYG в Azure Marketplace (начиная с сентября 2016 г.) автоматически задаются для новых серверов Azure RHUI, дополнительные действия не требуются.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>Временная шкала подключения новой инфраструктуры Azure RHUI
| Дата | Примечание |
| --- | --- |
| 22 сентября 2016 г. |Серверы RHUI и направления установки, доступные для использования. Виртуальные машины, развернутые с помощью новых образов RHEL PAYG из Azure Marketplace (выпущенных в сентябре 2016 г.), будут автоматически использовать новые серверы RHUI, но существующие виртуальные машины используются с согласия пользователя |
| 1 ноября 2016 г. |Образы предыдущих версий для виртуальных машин RHEL PAYG (использующие старые серверы Azure RHUI) будут удалены из коллекции Azure Marketplace |
| 16 января 2017 г. |Старые серверы Azure RHUI будут списаны. К этому моменту следует обновить все соответствующие виртуальные машины RHEL PAYG, чтобы получить доступ к Azure RHUI |

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>IP-адреса для новых серверов RHUI доставки содержимого
```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
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

### <a name="all-in-one-script-for-automating-the-above-task"></a>Сценарий "все в одном" для автоматизации описанной выше задачи
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
Инфраструктура RHUI поддерживается во всех регионах, где доступны предоставляемые по запросу образы RHEL. В настоящее время в этот список входят общедоступные регионы, указанные на странице [мониторинга состояния Azure](https://azure.microsoft.com/status/), а также регионы, обслуживающие государственные организации США. Доступ к RHUI для виртуальных машин, развернутых из предоставляемых по запросу образов RHEL, входит в стоимость образа. Так как в будущем планируется расширить регионы, в которых будут доступны предоставляемые по запросу экземпляры RHEL, сведения об их доступности по регионам и странам будут обновлены.

> [!NOTE]
> Доступ к размещенной в Azure инфраструктуре RHUI могут получать виртуальные машины с IP-адресами в рамках [диапазонов IP-адресов центра обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

## <a name="get-updates-from-another-update-repository"></a>Получение обновлений из другого репозитория обновлений
Чтобы получать обновления из другого репозитория обновлений (не RHUI, размещенного в Azure), необходимо отменить регистрацию экземпляров в RHUI и повторно зарегистрировать их в желаемой инфраструктуре обновления (например, Red Hat Network Satellite или на портале клиента CDN Red Hat). Кроме того, необходима подписка Red Hat для этих служб и регистрация [Red Hat Cloud Access в Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Чтобы отменить регистрацию RHUI и зарегистрироваться в другой инфраструктуре обновления, сделайте следующее:

1. Измените все `enabled=1` на `enabled=0` в репозитории /etc/yum.repos.d/rh-cloud.repo. Например:
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. Измените значение `enabled=0` на `enabled=1` в файле /etc/yum/pluginconf.d/rhnplugin.conf.
3. Затем зарегистрируйтесь в нужной инфраструктуре, например на портале клиента Red Hat. Дополнительные сведения о регистрации системы на портале клиента Red Hat и оформлении подписки см. в [руководстве по решению Red Hat](https://access.redhat.com/solutions/253273).

> [!NOTE]
> В стоимость образа RHEL с оплатой по мере использования (PAYG) входит плата за доступ к инфраструктуре RHUI, размещенной в Azure. Если отменить регистрацию виртуальной машины RHEL с оплатой по мере использования в инфраструктуре RHUI, размещенной в Azure, она не станет виртуальной машиной с использованием собственной лицензии (BYOL). Таким образом, после регистрации той же виртуальной машины в другом репозитории обновлений с вас может взиматься двойная плата. 
> 
> Если требуется использовать другую инфраструктуру обновлений (не RHUI, размещенную в Azure), рекомендуется создать и развернуть собственный образ (с использованием собственной лицензии), как описано в статье [Подготовка виртуальной машины на основе Red Hat для Azure](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о создании виртуальной машины Red Hat Enterprise Linux из образа с оплатой по мере использования и о применении размещенной в Azure RHUI см. на странице [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). В существующем экземпляре RHEL можно использовать `yum update` без дополнительной настройки.




<!--HONumber=Nov16_HO3-->


