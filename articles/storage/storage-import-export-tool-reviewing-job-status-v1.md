---
title: "Просмотр состояния задания с помощью файлов журнала копирования | Документация Майкрософт"
description: "Сведения об использовании файлов журнала, созданных при запуске задания импорта или экспорта, для просмотра состояния задания импорта и экспорта."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 25cd0308115994463bd50562594d4e2bb88e8286
ms.openlocfilehash: 66d7690249cb653dfb8d2e591d1ce66162d98a7e


---

# <a name="reviewing-job-status-with-copy-log-files"></a>Просмотр состояния задания с помощью файлов журнала копирования
При обработке дисков, связанных с заданием импорта или экспорта, служба импорта и экспорта Microsoft Azure записывает файлы журнала копирования в учетную запись хранения, в которую импортируются большие двоичные объекты или из которой они экспортируются. Файл журнала содержит подробные сведения о состоянии каждого импортируемого или экспортируемого файла. При запросе состояния завершенного задания возвращается URL-адрес для каждого файла журнала копирования. Дополнительные сведения см. [здесь](/rest/api/storageservices`Get-Job3).  
  
 Ниже приведены примеры URL-адресов файлов журнала копирования для задания импорта с двумя дисками.  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath /waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Дополнительные сведения о формате журналов копирования и полный список кодов состояний см. в статье [Import-Export Service Log File Format](storage-import-export-file-format-log.md) (Формат файла журнала службы импорта и экспорта).  
  
## <a name="see-also"></a>См. также  
 [Настройка средства импорта и экспорта Azure](storage-import-export-tool-setup-v1.md)   
 [Подготовка жестких дисков для задания импорта](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 [Repairing an Import Job](storage-import-export-tool-repairing-an-import-job-v1.md)  (Исправление задания импорта)  
 [Repairing an Export Job](storage-import-export-tool-repairing-an-export-job-v1.md)  (Исправление задания экспорта)  
 [Устранение неполадок со средством импорта и экспорта Azure](storage-import-export-tool-troubleshooting-v1.md)



<!--HONumber=Dec16_HO2-->


