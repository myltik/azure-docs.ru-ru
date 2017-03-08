---
title: "Просмотр задания импорта и экспорта Azure | Документация Майкрософт"
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
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: f0d445e51ff91f6edc219522ec3c12a86dc4ee98
ms.lasthandoff: 02/16/2017


---

# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Просмотр состояния задания импорта и экспорта Azure с помощью файлов журнала копирования
При обработке дисков, связанных с заданием импорта или экспорта, служба импорта и экспорта Microsoft Azure записывает файлы журнала копирования в учетную запись хранения, в которую импортируются большие двоичные объекты или из которой они экспортируются. Файл журнала содержит подробные сведения о состоянии каждого импортируемого или экспортируемого файла. При запросе состояния завершенного задания возвращается URL-адрес для каждого файла журнала копирования. Дополнительные сведения см. [здесь](/rest/api/storageservices`Get-Job3).  
  
 Ниже приведены примеры URL-адресов файлов журнала копирования для задания импорта с двумя дисками.  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath /waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Дополнительные сведения о формате журналов копирования и полный список кодов состояний см. в статье [Import-Export Service Log File Format](storage-import-export-file-format-log.md) (Формат файла журнала службы импорта и экспорта).  
  
## <a name="see-also"></a>См. также  
 [Setting Up the Azure Import-Export Tool](storage-import-export-tool-setup-v1.md)  (Настройка средства импорта и экспорта Azure)  
 [Подготовка жестких дисков для задания импорта](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 [Repairing an Import Job](storage-import-export-tool-repairing-an-import-job-v1.md)  (Исправление задания импорта)  
 [Repairing an Export Job](storage-import-export-tool-repairing-an-export-job-v1.md)  (Исправление задания экспорта)  
 [Troubleshooting the Azure Import-Export Tool](storage-import-export-tool-troubleshooting-v1.md) (Устранение неполадок со средством импорта и экспорта Azure)

