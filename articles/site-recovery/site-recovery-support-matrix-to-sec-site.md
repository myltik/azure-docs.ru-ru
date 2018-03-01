---
title: "Таблица поддержки для репликации на дополнительный сайт с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье перечислены операционные системы и компоненты, поддерживаемые Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: raynew
ms.openlocfilehash: 256bad0c3c06182b6be2b647ae27db90fe69724d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Таблица поддержки для репликации на дополнительный сайт с помощью Azure Site Recovery

В этой статье перечислены поддерживаемые конфигурации и компоненты при использовании службы [Azure Site Recovery](site-recovery-overview.md) для репликации на дополнительный локальный сайт.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.

**Развертывание** | **Дополнительные сведения** 
--- | ---
**Из VMware в VMware** | Аварийное восстановление локальных виртуальных машин VMware на дополнительный сайт VMware.<br/><br/> Скачайте [руководство пользователя InMage Scout](https://aka.ms/asr-scout-user-guide).
**Из Hyper-V в Hyper-V** | Аварийное восстановление локальных виртуальных машин Hyper-V (в облаках VMM) на дополнительный сайт VMM.<br></br> Не поддерживается без VMM.



  

## <a name="host-servers"></a>Серверы узлов

**Развертывание** | **Поддержка**
--- | ---
**Виртуальная машина VMware или физический сервер** | vCenter 5.5, 6.0 или 6.5 (поддержка только функций vCenter 5.5)
**Hyper-V с VMM** | Windows Server 2016 и Windows Server 2012 R2 с последними обновлениями.<br/><br/> Узлами Windows Server 2016 должен управлять сервер VMM 2016.<br/><br/> Сейчас облака VMM 2016, сочетающие узлы Windows Server 2016 и 2012 R2, не поддерживаются.<br/><br/> Развертывание, включающее в себя обновление существующей среды VMM 2012 R2 до System Center 2016, сейчас не поддерживаются.


## <a name="support-for-replicated-machine-os-versions"></a>Поддержка версий ОС для реплицируемых виртуальных машин

В следующей таблице указаны сведения о поддержке операционных систем для компьютеров, реплицируемых с помощью Site Recovery. В поддерживаемой операционной системе может выполняться любая рабочая нагрузка.

**VMware или физический сервер** | **Hyper-V (с VMM)**
--- | ---
64-разрядная версия Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 как минимум с пакетом обновления 1 (SP1).<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1 или 7.2. <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1 или 7.2. <br/><br/> Oracle Enterprise Linux 6.4, 6.5 или 6.8 с ядром, совместимым с Red Hat, или с ядром Unbreakable Enterprise Kernel Release 3 (UEK3). <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4  | Любая операционная система на виртуальной машине, [ поддерживаемая Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Хранилище компьютера Linux

Можно реплицировать только компьютеры Linux со следующими характеристиками хранилища:

- файловая система: EXT3, ETX4, ReiserFS, XFS;
- программное обеспечение Multipath — Device Mapper;
- диспетчер томов: LVM2.
- Физические серверы с хранилищем контроллера HP CCISS не поддерживаются.
- Файловая система ReiserFS поддерживается только в SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3).

## <a name="network-configuration"></a>Конфигурация сети

### <a name="hosts"></a>Узлы

**Конфигурация** | **VMware или физический сервер** | **Hyper-V (с VMM)**
--- | --- | ---
Объединение сетевых адаптеров | Yes | Yes
Виртуальная локальная сеть | Yes | Yes
IPv4 | Yes | Yes
IPv6 | Нет  | Нет 

### <a name="guest-vms"></a>Гостевые виртуальные машины

**Конфигурация** | **VMware или физический сервер** | **Hyper-V (с VMM)**
--- | --- | ---
Объединение сетевых адаптеров | Нет  | Нет 
IPv4 | Yes | Yes
IPv6 | Нет  | Нет 
Статический IP-адрес (Windows) | Yes | Yes
Статический IP-адрес (Linux) | Yes | Yes
Несколько сетевых адаптеров | Yes | Yes


## <a name="storage"></a>Хранилище

### <a name="host-storage"></a>Хранилище узла

**Устройство или система хранения (узел)** | **VMware или физический сервер** | **Hyper-V (с VMM)**
--- | --- | ---
NFS | Yes | Недоступно
SMB 3.0 | Недоступно | Yes
Сеть SAN (iSCSI) | Yes | Yes
Многопутевой (MPIO) | Yes | Yes

### <a name="guest-or-physical-server-storage"></a>Хранилища гостевого или физического сервера

**Конфигурация** | **VMware или физический сервер** | **Hyper-V (с VMM)**
--- | --- | ---
VMDK | Yes | Недоступно
VHD (VHDX) | Недоступно | Да (до 16 дисков)
Виртуальная машина 2-го поколения | Недоступно | Yes
Общий диск кластера | Yes  | Нет 
Зашифрованный диск | Нет  | Нет 
UEFI| Yes | Недоступно
NFS | Нет  | Нет 
SMB 3.0 | Нет  | Нет 
RDM | Yes | Недоступно
Диск > 1 ТБ | Yes | Yes
Том с чередующимся диском > 1 ТБ<br/><br/> Диспетчер логических томов | Yes | Yes
Дисковые пространства | Нет  | Yes
"Горячее" добавление или удаление диска | Yes | Нет 
Исключение диска | Yes | Yes
Многопутевой (MPIO) | Недоступно | Yes

## <a name="vaults"></a>Хранилища

**Действие** | **VMware или физический сервер** | **Hyper-V (с VMM)**
--- | --- | ---
Перемещение хранилищ между группами ресурсов (в пределах подписок и между ними) | Нет  | Нет 
Перемещение хранилища, сети или виртуальных машин Azure между группами ресурсов (в пределах подписок и между ними) | Нет  | Нет 

## <a name="provider-and-agent"></a>Поставщик и агент

**Имя** | **Описание** | **Последняя версия** | **Дополнительные сведения**
--- | --- | --- | --- | ---
**Поставщик Azure Site Recovery** | Координирует взаимодействие между локальными серверами и Azure <br/><br/> Устанавливается на локальных серверах VMM или серверах Hyper-V, если сервера VMM нет. | 5.1.19 ([доступна на портале](http://aka.ms/downloaddra)) | [Новейшие функции и последние исправления](https://support.microsoft.com/kb/3155002)
**Служба Mobility Service** | Координирует репликацию между локальными серверами VMware или физическими серверами и дополнительным сайтом.<br/><br/> Устанавливается на виртуальной машине VMware или физических серверах, репликацию которых необходимо выполнить.  | Нет (доступна на портале) | Недоступно


## <a name="next-steps"></a>Дополнительная информация

- [Репликация виртуальных машин Hyper-V из облаков VMM на вторичный сайт VMM](tutorial-vmm-to-vmm.md)
- [Репликация виртуальных машин и физических серверов VMware на дополнительный сайт](tutorial-vmware-to-vmware.md)
