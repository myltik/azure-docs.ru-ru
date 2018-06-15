---
title: Настройка инструмента импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как настроить инструмент подготовки и исправления дисков для службы импорта и экспорта Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 6b5febd051d0b956c90cb14c260dda1881adac3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059769"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Настройка инструмента импорта и экспорта Azure

Инструмент импорта и экспорта Microsoft Azure используется в службе импорта и экспорта Microsoft Azure для подготовки и исправления дисков. Это средство может выполнять перечисленные ниже функции.

* С его помощью перед созданием задания импорта можно скопировать данные на жесткие диски, которые будут отправлены в центр обработки данных Azure.
* После завершения задания импорта это средство можно использовать для восстановления поврежденных, отсутствующих или конфликтных больших двоичных объектов.
* Это средство также позволяет восстанавливать на дисках поврежденные или отсутствующие файлы после завершения задания экспорта.

## <a name="prerequisites"></a>предварительным требованиям

При **подготовке дисков** для задания импорта необходимо выполнить следующие условия.

* У вас должна быть активная подписка Azure.
* Она должна содержать учетную запись хранения с достаточным объемом свободного пространства для хранения файлов, которые будут импортированы.
* Требуется по крайней мере один из ключей доступа учетной записи хранения.
* Необходим компьютер ("компьютер для копирования") с Windows 7, Windows Server 2008 R2 или более новой операционной системой Windows.
* На этом компьютере должен быть установлен компонент .NET Framework 4.
* На компьютере для копирования должна быть включена служба BitLocker.
* Вам потребуются один или несколько пустых 2,5- или 3,5-дюймовых дисков SATA II или SATA III либо твердотельные накопители, подключенные к целевому компьютеру.
* Файлы, которые нужно импортировать, должны быть доступными на целевом компьютере, независимо от того, где они сохранены: в сетевой папке или на локальном жестком диске.

Если вы пытаетесь **исправить операцию импорта**, которая была частично не выполнена, вам потребуется следующее:

* файлы журнала копирования;
* ключ учетной записи хранения.

Если вы пытаетесь **исправить операцию экспорта**, которая была частично не выполнена, вам потребуется следующее:

* файлы журнала копирования;
* файлы манифеста (необязательно);
* ключ учетной записи хранения.

## <a name="installing-the-azure-importexport-tool"></a>Установка средства импорта и экспорта Azure

Сначала [скачайте инструмент импорта и экспорта Azure](https://www.microsoft.com/download/details.aspx?id=55280) и извлеките его в каталог на компьютере, например `c:\WAImportExport`.

Инструмент импорта и экспорта Azure состоит из следующих файлов:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

После этого откройте окно командной строки с **правами администратора** и перейдите в каталог, содержащий извлеченные файлы.

Чтобы вывести справку по командам, запустите инструмент (`WAImportExport.exe`) без параметров.

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Дополнительная информация

* [Подготовка жестких дисков для задания импорта](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Предварительный просмотр использования дисков для задания экспорта](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Просмотр состояния задания с помощью файлов журнала копирования](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Подготовка задания импорта](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Подготовка задания экспорта](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Устранение неполадок со средством импорта и экспорта Azure](storage-import-export-tool-troubleshooting-v1.md)
