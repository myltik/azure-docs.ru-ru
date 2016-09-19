<properties
	pageTitle="Таблица поддержки Azure Site Recovery | Microsoft Azure"
	description="В этой статье перечислены операционные системы и компоненты, поддерживаемые Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/07/2016"
	ms.author="raynew"/>

# Таблица поддержки Azure Site Recovery

В этой статье перечислены операционные системы и компоненты, поддерживаемые для развертываний Azure Site Recovery. Список поддерживаемых и необходимых компонентов для каждого сценария развертывания указан в соответствующей статье о развертывании. Данный раздел содержит итоговую информацию о них.

## Поддерживаемые операционные системы для серверов виртуализации


**Репликация** | **Что реплицируется** | **Цель репликации** | **ОС узла**
---|---|---|--- 
**Узлы Hyper-V** | Любая рабочая нагрузка. | Таблицы Azure | Windows Server 2012 R2 с последними обновлениями.
**Узлы Hyper-V в облаках VMM** | Любая рабочая нагрузка. | Таблицы Azure | Windows Server 2012 R2 с последними обновлениями.
**Узлы Hyper-V в облаках VMM** | Любая рабочая нагрузка. | Дополнительный сайт VMM | По крайней мере Windows Server 2012 с последними обновлениями
**Узлы VMware или vCenter** | Любая рабочая нагрузка. | Таблицы Azure | vCenter 5.5 или vCenter 6.0 (поддержка только функций vCenter 5.5) <br/><br/> vSphere 6.0, vCenter 5.5 или vCenter 5.1 с последними обновлениями.
**Узлы VMware или vCenter** | Любая рабочая нагрузка. | Дополнительный сайт VMware | vCenter 5.5 или vCenter 6.0 (поддержка только функций vCenter 5.5) <br/><br/> vSphere 6.0, vCenter 5.5 или vCenter 5.1 с последними обновлениями.

## Требования к поддерживаемым реплицируемым виртуальным машинам

**Репликация** | **Что реплицируется** | **Цель репликации** | **ОС узла**
---|---|---|--- 
**Виртуальные машины Hyper-V** | Любая рабочая нагрузка. | Таблицы Azure | Любая гостевая ОС, [поддерживаемая Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Виртуальные машины должны соответствовать [требованиям Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
**Виртуальные машины Hyper-V в облаке VMM** | Любая рабочая нагрузка. | Таблицы Azure | Любая гостевая ОС, [поддерживаемая Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Виртуальные машины должны соответствовать [требованиям Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
**Виртуальные машины Hyper-V в облаках VMM** | Любая рабочая нагрузка. | Дополнительный сайт VMM | Любая гостевая ОС, [поддерживаемая Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
**Виртуальные машины VMware** | Любая рабочая нагрузка, выполняемая на виртуальной машине Windows. | Таблицы Azure | 64-разрядная версия Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 как минимум с пакетом обновления 1 (SP1).<br/><br/> Виртуальные машины должны соответствовать [требованиям Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
**Виртуальные машины VMware** | Любая рабочая нагрузка, выполняемая на виртуальной машине Linux. | Таблицы Azure | Red Hat Enterprise Linux версий 6.7, 7.1, 7,2. <br/><br/> Centos версий 6.5, 6.6 6.7, 7.0, 7.1, 7,2. <br/><br/> Oracle Enterprise Linux версии 6.4 или 6.5 с Unbreakable Enterprise Kernel Release 3 (UEK3) или совместимым ядром Red Hat. <br/><br/> SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3). <br/><br/> Необходимое хранилище: файловая система (EXT3, ETX4, ReiserFS, XFS); многопутевое ПО: Device Mapper (режим Multipath)); диспетчер томов: (LVM2). Физические серверы с хранилищем контроллера HP CCISS не поддерживаются. Файловая система ReiserFS поддерживается только в SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3).<br/><br/> Виртуальные машины должны соответствовать [требованиям Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
**Виртуальные машины VMware** | Любая рабочая нагрузка, выполняемая на виртуальной машине Windows. | Дополнительный сайт VMware | 64-разрядная версия Windows Server 2012 R2, Windows Server 2012 или Windows Server 2008 R2 как минимум с пакетом обновлений 1 (SP1).
**Виртуальные машины VMware** | Любая рабочая нагрузка, выполняемая на виртуальной машине Linux. | Дополнительный сайт VMware | Red Hat Enterprise Linux версий 6.7, 7.1, 7,2. <br/><br/> Centos версий 6.5, 6.6 6.7, 7.0, 7.1, 7,2. <br/><br/> Oracle Enterprise Linux версии 6.4 или 6.5 с Unbreakable Enterprise Kernel Release 3 (UEK3) или совместимым ядром Red Hat. <br/><br/> SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3). <br/><br/> Необходимое хранилище: файловая система (EXT3, ETX4, ReiserFS, XFS); многопутевое ПО: Device Mapper (режим Multipath)); диспетчер томов: (LVM2). Физические серверы с хранилищем контроллера HP CCISS не поддерживаются. Файловая система ReiserFS поддерживается только в SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3).
**Физические серверы** | Любая рабочая нагрузка, выполняемая в Windows. | Таблицы Azure | 64-разрядная версия Windows Server 2012 R2, Windows Server 2012 или Windows Server 2008 SP1 как минимум с пакетом обновления 1 (SP1).
**Физические серверы** | Любая рабочая нагрузка, выполняемая в Linux. | Таблицы Azure | Red Hat Enterprise Linux версий 6.7, 7.1, 7.2. <br/><br/> CentOS версий 6.5, 6.6, 6.7, 7.0, 7.1, 7.2. <br/><br/> Oracle Enterprise Linux версии 6.4 или 6.5 с Unbreakable Enterprise Kernel Release 3 (UEK3) или совместимым ядром Red Hat. <br/><br/> SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3). <br/><br/> Необходимое хранилище: файловая система (EXT3, ETX4, ReiserFS, XFS); многопутевое ПО: Device Mapper (режим Multipath)); диспетчер томов: (LVM2). Физические серверы с хранилищем контроллера HP CCISS не поддерживаются. Файловая система ReiserFS поддерживается только в SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3).
**Физические серверы** | Любая рабочая нагрузка, выполняемая в Windows. | Вторичный сайт | 64-разрядная версия Windows Server 2012 R2, Windows Server 2012 или Windows Server 2008 SP1 как минимум с пакетом обновления 1 (SP1).
**Физические серверы** | Любая рабочая нагрузка, выполняемая в Linux. | Вторичный сайт | Red Hat Enterprise Linux версий 6.7, 7.1, 7.2. <br/><br/> CentOS версий 6.5, 6.6, 6.7, 7.0, 7.1, 7.2. <br/><br/> Oracle Enterprise Linux версии 6.4 или 6.5 с Unbreakable Enterprise Kernel Release 3 (UEK3) или совместимым ядром Red Hat. <br/><br/> SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3). <br/><br/> Необходимое хранилище: файловая система (EXT3, ETX4, ReiserFS, XFS); многопутевое ПО: Device Mapper (режим Multipath)); диспетчер томов: (LVM2). Физические серверы с хранилищем контроллера HP CCISS не поддерживаются. Файловая система ReiserFS поддерживается только в SUSE Linux Enterprise Server 11 с пакетом обновления 3 (SP3).


## Версии поставщика

**Имя** | **Описание** | **Последняя версия** | **Поддержка** | **Дополнительные сведения**
---|---|---|---| ---
**Поставщик Azure Site Recovery** | Координирует взаимодействие между локальными серверами и Azure или вторичным сайтом. <br/><br/> Устанавливается на локальных серверах VMM или серверах Hyper-V, если сервер VMM отсутствует. | 5\.1.1700 (доступна на портале) | [Новейшие функции и последние исправления](https://support.microsoft.com/kb/3155002)
**Унифицированная установка Azure Site Recovery (VMware в Azure)** | Координирует взаимодействие между локальными серверами VMware и Azure. <br/><br/> Устанавливается на локальных серверах VMware. | 9\.3.4246.1 (доступна на портале) | [Новейшие функции и последние исправления](https://support.microsoft.com/kb/3155002)
**Служба Mobility Service** | Координирует репликацию между локальными серверами VMware или физическими серверами и Azure или вторичным сайтом. | Нет (доступна на портале) | Устанавливается на каждой виртуальной машине VMware или физическом сервере, репликацию которого нужно выполнить. **Агент служб восстановления Microsoft Azure (MARS)** | Координирует репликацию между виртуальными машинами Hyper-V и Azure.<br/><br/> Устанавливается на локальных серверах Hyper-V (с сервером VMM или без него). | 2\.0.8689.0 (доступна на портале) | Этот агент используется Azure Site Recovery и службой архивации Azure. [Подробнее](https://support.microsoft.com/ru-RU/kb/2997692)

## Дальнейшие действия

[Подготовка к развертыванию](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0907_2016-->