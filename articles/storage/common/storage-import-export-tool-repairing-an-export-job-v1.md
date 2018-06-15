---
title: Восстановление задания экспорта инструмента импорта и экспорта Azure версии 1 | Документация Майкрософт
description: Узнайте, как восстановить задание экспорта, созданное и выполняемое с помощью службы импорта и экспорта Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 57ab58fa1fd8371d0b6f019f94bb162bcc1e0e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059679"
---
# <a name="repairing-an-export-job"></a>Исправление задания экспорта
После завершения задания экспорта вы можете локально выполнить следующие задачи с помощью инструмента импорта и экспорта Microsoft Azure:  
  
1.  загрузить любые файлы, которые служба импорта и экспорта Azure не смогла экспортировать;  
  
2.  проверить правильность экспорта файлов с диска.  
  
Для использования этой возможности требуется подключение к службе хранилища Azure.  
  
Для восстановления задания экспорта используется команда **RepairExport**.

## <a name="repairexport-parameters"></a>Параметры RepairExport

Для команды **RepairExport** можно определить следующие параметры.  
  
|Параметр|ОПИСАНИЕ|  
|---------------|-----------------|  
|**/r:<файл_восстановления\>**|Обязательный элемент. Путь к файлу восстановления для отслеживания восстановления и возобновления прерванной операции восстановления. Каждый диск должен быть связан с одним и только одним файлом восстановления. Запуская восстановление для определенного диска, вы передаете путь к файлу восстановления, который еще не существует. Чтобы возобновить прерванную операцию восстановления, необходимо передать имя существующего файла восстановления. Всегда необходимо указывать файл восстановления, соответствующий целевому диску.|  
|**/logdir:<каталог_журнала\>**|Необязательный элемент. Каталог журнала. В этот каталог записываются файлы подробного журнала. Если каталог журнала не указан, вместо него будет использоваться текущий каталог.|  
|**/d:<целевой_каталог\>**|Обязательный элемент. Каталог, который нужно проверить и восстановить. Обычно это корневой каталог экспортируемого диска, но также может быть общая сетевая папка, в которой хранится копия экспортированных файлов.|  
|**/bk:<ключ_BitLocker\>**|Необязательный элемент. Укажите ключ BitLocker, чтобы разблокировать зашифрованное размещение, в котором хранятся экспортированные файлы.|  
|**/sn:<учетная_запись_хранения\>**|Обязательный элемент. Имя учетной записи хранения для задания экспорта.|  
|**/sk:<ключ_учетной_записи_хранения\>**|**Требуется**, только если не указан SAS контейнера. Ключ учетной записи хранения для задания экспорта.|  
|**/csas:<SAS_контейнера\>**|**Требуется**, только если не указан ключ учетной записи хранения. Контейнер SAS для доступа к большим двоичным объектам, связанным с заданием экспорта.|  
|**/CopyLogFile:<файл_журнала\>**|Обязательный элемент. Путь к файлу журнала копирования диска. Этот файл создается службой импорта и экспорта Windows Azure. Его можно скачать из хранилища BLOB-объектов, связанного с заданием. Файл журнала копирования содержит сведения о BLOB-объектах или файлах с ошибками, которые требуется восстановить.|  
|**/ ManifestFile:<файл_манифеста_диска\>**|Необязательный элемент. Путь к файлу манифеста экспортируемого диска. Этот файл создается службой импорта и экспорта Windows Azure и сохраняется на экспортируемом диске, а если указано отдельно — еще и в большом двоичном объекте в учетной записи хранения, связанной с заданием.<br /><br /> Содержимое файлов на экспортируемом диске проверяется с помощью MD5-хэшей, содержащихся в этом файле. Все файлы, в которых обнаружатся повреждения, будут повторно загружены и записаны в целевые каталоги.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Использование режима RepairExport для исправления проблем, возникших при экспорте  
С помощью инструмента импорта и экспорта Azure вы можете скачать файлы, которые не удалось экспортировать. Файл журнала копирования будет содержать список файлов, которые не удалось экспортировать.  
  
Среди прочего, возможны следующие причины проблем при экспорте:  
  
-   повреждение дисков;  
  
-   изменение ключа учетной записи хранения во время передачи.  
  
Перед запуском средства в режиме **RepairExport** следует подключить к компьютеру диск, содержащий экспортированные файлы. Затем запустите инструмент импорта и экспорта Azure, указав путь к этому диску с помощью параметра `/d`. Также необходимо указать путь к загруженному файлу журнала копирования диска. Следующий пример командной строки запускает средство для восстановления всех файлов, которые не удалось экспортировать:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
В следующем примере представлен файл журнала копирования с информацией о том, что один блок в большом двоичном объекте не удалось экспортировать:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Файл журнала копирования указывает на сбой при загрузке службой импорта и экспорта Windows Azure одного из блоков большого двоичного объекта в файл на экспортируемом диске. Остальные компоненты файла загружены успешно, и длина файла установлена правильно. В этом случае средство откроет файл на диске, загрузит блок из учетной записи хранения и запишет этот блок длиной 65536 в файл, начиная со смещения 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Использование RepairExport для проверки содержимого диска  
Также средство импорта и экспорта Azure в режиме **RepairExport** можно использовать для проверки правильности содержимого на диске. Файл манифеста на каждом экспортируемом диске содержит MD5-хэши всего содержимого диска.  
  
Служба импорта и экспорта Azure может в процессе экспорта сохранять эти файлы манифеста в учетную запись хранения. Расположение файлов манифеста можно получить после завершения задания с помощью команды [получения задания](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). Дополнительные сведения о формате файла манифеста диска см. в статье [Формат файла манифеста службы импорта и экспорта](storage-import-export-file-format-metadata-and-properties.md).  
  
В следующем примере показано, как запустить инструмент импорта и экспорта Azure с параметрами **/ManifestFile** и **/CopyLogFile**.  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Ниже приведен пример файла манифеста:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Завершив процесс восстановления, средство считывает каждый файл, указанный в файле манифеста, и проверяет целостность этих файлов с помощью MD5-хэшей. Если использовать указанный выше манифест, будут проверены следующие компоненты.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Средство загрузит заново любой компонент, который не прошел проверку, и запишет его в тот же файл на диске.  
  
## <a name="next-steps"></a>Дополнительная информация
 
* [Настройка средства импорта и экспорта Azure](storage-import-export-tool-setup-v1.md)   
* [Подготовка жестких дисков для задания импорта](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Просмотр состояния задания с помощью файлов журнала копирования](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Подготовка задания импорта](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Устранение неполадок со средством импорта и экспорта Azure](storage-import-export-tool-troubleshooting-v1.md)
