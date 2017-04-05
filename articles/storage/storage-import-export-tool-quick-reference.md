---
title: "Краткий справочник по командам для заданий импорта инструмента импорта и экспорта Azure | Документация Майкрософт"
description: "Справочник по командам инструмента импорта и экспорта Azure, которые наиболее часто используются для заданий импорта."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e9377e0c5001cf5be220e19e06ff96c1e058e853
ms.lasthandoff: 03/30/2017


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Краткий справочник по часто используемым командам для заданий импорта

В этой статье приводится краткая справочная информация о некоторых часто используемых командах. Дополнительные сведения об использовании см. в разделе [Подготовка жестких дисков для задания импорта](storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>Первый сеанс

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Второй сеанс

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Прерывание последнего сеанса

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Возобновление последнего прерванного сеанса

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Добавление дисков к последнему сеансу

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Дальнейшие действия

* [Пример рабочего процесса по подготовке жестких дисков для задания импорта](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)

