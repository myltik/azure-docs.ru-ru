---
title: Резервное копирование файлов и приложений Azure Stack | Документация Майкрософт
description: Использование службы Azure Backup для резервного копирования и восстановления файлов и приложений Azure Stack в среду Azure Stack.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 99ac43efa5d3211bbe2d790f28532e682058313c
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075883"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Резервное копирование файлов и приложений в Azure Stack
Службу Azure Backup можно использовать для защиты (или резервного копирования) файлов и приложений в Azure Stack. Чтобы создать резервные копии файлов и приложений, установите Microsoft Azure Backup Server в качестве виртуальной машины, выполняющейся в Azure Stack. Вы можете защитить любые приложения, запущенные на любом сервере Azure Stack в той же виртуальной сети. Установив Azure Backup Server, добавьте диски Azure, чтобы увеличить локальное хранилище, доступное для краткосрочного резервного копирования данных. Azure Backup Server использует хранилища Azure для долгосрочного хранения.

> [!NOTE]
> Хотя Azure Backup Server и System Center Data Protection Manager (DPM) похожи, DPM нельзя использовать с Azure Stack.
>


## <a name="azure-backup-server-protection-matrix"></a>Таблица защиты посредством Azure Backup Server
Azure Backup Server защищает следующие рабочие нагрузки виртуальных машин Azure Stack.

| Защищенный источник данных | Защита и восстановление |
| --------------------- | ----------------------- |
| Windows Server Semi Annual Channel: Datacenter/Enterprise/Standard | Тома, файлы, папки |
| Windows Server 2016: Datacenter/Enterprise/Standard | Тома, файлы, папки |
| Windows Server 2012 R2: Datacenter/Enterprise/Standard | Тома, файлы, папки |
| Windows Server 2012: Datacenter/Entprise/Standard | Тома, файлы, папки |
| Windows Server 2008 R2: Datacenter/Enterprise/Standard | Тома, файлы, папки |
| SQL Server 2016 | База данных |
| SQL Server 2014 | База данных |
| SQL Server 2012 с пакетом обновления 1 (SP1) | База данных |
| SharePoint 2013 | Ферма, база данных, внешний интерфейс, веб-сервер |
| SharePoint 2010 | Ферма, база данных, внешний интерфейс, веб-сервер |


## <a name="install-azure-backup-server"></a>Установка Azure Backup Server
Инструкции по установке Azure Backup Server см. в руководстве по [подготовке к резервному копированию рабочих нагрузок с использованием Azure Backup Server](backup-azure-microsoft-azure-backup.md). Перед установкой и настройкой Azure Backup Server учитывайте следующее:

### <a name="determining-size-of-virtual-machine"></a>Определение размера виртуальной машины
Для запуска Azure Backup Server на виртуальных машинах Azure Stack используйте размер не ниже А2. Чтобы выбрать размер виртуальной машины, скачайте [калькулятор размеров виртуальных машин Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Виртуальные сети в виртуальных машинах Azure Stack
Все виртуальные машины, используемые в рабочей нагрузке Azure Stack, должны принадлежать к одной виртуальной сети и подписке Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Хранение данных резервных копий на локальном диске и в Azure
Для оперативного восстановления Azure Backup Server хранит данные резервного копирования на дисках Azure, присоединенных к виртуальной машине. После присоединения к виртуальной машине дисков и дискового пространства хранилищем будет управлять Azure Backup Server. Объем хранилища резервных копий данных зависит от количества и размера дисков, подключенных к каждой [виртуальной машине Azure Stack](../azure-stack/user/azure-stack-storage-overview.md). Для всех размеров виртуальных машин Azure Stack предусмотрено максимальное число дисков, которые можно подключить. Например, для размера A2 предусмотрено четыре жестких диска. Для A3 — восемь дисков. Для A4 — 16 дисков. Опять-таки, размер и число дисков определяет общий пул хранилища резервных копий.

> [!IMPORTANT]
> Хранить данные оперативного восстановления (резервной копии) на дисках, присоединенных к Azure Backup Server, более пяти дней **нельзя**.
>

Хранение резервных копий данных в Azure ограничивает инфраструктуру резервного копирования в Azure Stack. Если данным больше пяти дней, они должны храниться в Azure.

Чтобы хранить данные резервного копирования в Azure, создайте или используйте хранилище служб восстановления. При подготовке рабочей нагрузки резервного копирования Azure Backup Server вы [настроите хранилище служб восстановления](backup-azure-microsoft-azure-backup.md#recovery-services-vault). После этого при каждом выполнении задания резервного копирования в хранилище будет создаваться точка восстановления. Каждое хранилище служб восстановления содержит до 9999 точек восстановления. В зависимости от числа точек восстановления и срока их хранения, резервные копии данных можно хранить в течение многих лет. Например, можно создавать ежемесячные точки восстановления, которые будут храниться в течение пяти лет.
 
### <a name="using-sql-server"></a>Использование SQL Server
Если вы хотите использовать удаленный экземпляр SQL Server для базы данных Azure Backup Server, выберите только виртуальную машину Azure Stack под управлением SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Производительность виртуальных машин Azure Backup Server
Если используются и другие виртуальные машины, размер учетной записи хранения и ограничения на число операций ввода-вывода в секунду могут повлиять на производительность виртуальной машины Azure Backup Server. По этой причине следует использовать отдельную учетную запись хранилища для виртуальных машин Azure Backup Server. Агенту Azure Backup под управлением Azure Backup Server требуется временное хранилище:
    - для внутреннего использования (расположение кэша);
    - для данных, восстановленных из облака (локальная промежуточная область).
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Настройка временного дискового хранилища Azure Backup
Каждая виртуальная машина Azure Stack поставляется с временным хранилищем дисков, которое доступно для пользователя в качестве тома `D:\`. Локальную промежуточную область, которая требуется службе Azure Backup, можно разместить на диске `D:\`, а расположение кэша — на диске `C:\`. Таким образом, место на дисках, подключенных к виртуальной машине Azure Backup Server, не будет использоваться для других целей.

### <a name="scaling-deployment"></a>Масштабирование развертывания
Чтобы масштабировать развертывание, доступны следующие параметры:
  - Вертикальное масштабирование — увеличение размера виртуальной машины Azure Backup Server от серии А до серии D и увеличение локального хранилища [согласно инструкциям по настройке виртуальной машины Azure Stack](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Разгрузка данных — перенос старых данных в Azure Backup Server и хранение только последних данных в хранилище, подключенном к Azure Backup Server.
  - Горизонтальное масштабирование — добавление новых экземпляров Azure Backup Server для защиты рабочих нагрузок.

## <a name="see-also"></a>См. также
Сведения об использовании Azure Backup Server для защиты других рабочих нагрузок см. в следующих статьях:
- [Резервное копирование фермы SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Резервное копирование SQL Server](backup-azure-sql-mabs.md)
