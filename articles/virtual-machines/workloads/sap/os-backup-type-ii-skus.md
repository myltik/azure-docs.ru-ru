---
title: Резервное копирование и восстановление операционной системы SAP HANA в Azure (большие экземпляры) для номеров SKU типа II | Документация Майкрософт
description: Выполнение резервного копирования и восстановления операционной системы SAP HANA в Azure (большие экземпляры) для номеров SKU типа II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d5caf5836b96555e01b55d408e51f3df2407d35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657610"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Резервное копирование и восстановление операционной системы для номеров SKU типа II

В этой статье описывается процедура архивации и восстановления операционной системы для **номеров SKU типа II** больших экземпляров HANA. 

>[!NOTE]
>Для сценариев резервного копирования операционной системы используется предварительно установленное на сервере программное обеспечение ReaR.  

Как только специалисты службы Microsoft Service Management выполнят подготовку, по умолчанию на сервере настраивается несколько расписаний архивации всей операционной системы. С помощью команды ниже вы можете проверить расписание задания архивации:
```
#crontab –l
```
С помощью команды ниже вы можете в любое время изменить это расписание:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Как создать резервную копию вручную?

Резервное копирование операционной системы планируется с помощью **задания cron**. Тем не менее вы также можете выполнить резервное копирование операционной системы вручную. Для этого выполните команду ниже:

```
#rear -v mkbackup
```
На снимке экрана ниже показан пример создания резервной копии вручную:

![пример](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Как восстановить резервную копию?

Вы можете восстановить полную резервную копию или отдельный файл из нее. Для этого воспользуйтесь следующей командой:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
После операции восстановления файл будет восстановлен в текущем рабочем каталоге.

С помощью команды ниже показано восстановление файла */etc/fstabfrom* из файла резервной копии *backup.tar.gz*.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Вам необходимо скопировать файл в нужное расположение после его восстановления из файла резервной копии.

На снимке экрана ниже показано восстановление полной резервной копии:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Как установить инструмент ReaR и изменить конфигурацию? 

Для **номеров SKU типа II** больших экземпляров HANA пакеты Relax-and-Recover (ReaR) **предварительно установлены**, поэтому никакие дополнительные действия с вашей стороны не требуются. Для резервного копирования операционной системы вы можете напрямую запустить ReaR.
Тем не менее в случаях, когда требуется отдельная установка пакетов, вы можете выполнить шаги ниже, чтобы установить и настроить инструмент ReaR.

Чтобы установить пакеты резервного копирования **ReaR**, выполните команды ниже:

Для операционной системы **SLES** используйте следующую команду:
```
#zypper install <rear rpm package>
```
Для операционной системы **RHEL** используйте следующую команду: 
```
#yum install rear -y
```
Чтобы настроить инструмент ReaR, вам необходимо обновить параметры **OUTPUT_URL** и **BACKUP_URL** в *файле /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

На снимке экрана ниже показано восстановление полной резервной копии: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
