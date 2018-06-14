---
title: Формат файла журнала импорта и экспорта Azure | Документация Майкрософт
description: Изучите формат файлов журналов, созданных при выполнении задания службы импорта и экспорта.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23060009"
---
# <a name="azure-importexport-service-log-file-format"></a>Формат файла журнала службы импорта и экспорта Azure
Когда служба импорта и экспорта Microsoft Azure выполняет действия на диске в ходе задания импорта или экспорта, в блочные BLOB-объекты в учетной записи хранения, связанной с этим заданием, записываются журналы.  
  
Служба импорта и экспорта может записывать два журнала:  
  
-   журнал ошибок, который всегда создается в случае возникновения ошибки;  
  
-   подробный журнал, который не включен по умолчанию. Чтобы включить его, установите свойство `EnableVerboseLog` для операции [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) (Поместить задание) или [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) (Обновить свойства задания).  
  
## <a name="log-file-location"></a>Расположение файла журнала  
Журналы записываются в блочные BLOB-объекты в контейнере или виртуальном каталоге, указанном в параметре `ImportExportStatesPath`, который можно установить для операции `Put Job`. Расположение, в которое записываются журналы, зависит от того, как задана проверка подлинности задания, а также от значения, установленного для параметра `ImportExportStatesPath`. Проверка подлинности для задания может быть задана с использованием ключа учетной записи хранения или SAS (подписанного URL-адреса) контейнера.  
  
В качестве имени контейнера или виртуального каталога можно использовать имя по умолчанию (`waimportexport`) или любое другое имя.  
  
В следующей таблице приведены возможные варианты.  
  
|Метод проверки подлинности|Значение параметра `ImportExportStatesPath`Element|Расположение больших двоичных объектов журналов|  
|---------------------------|----------------------------------------------|---------------------------|  
|Ключ учетной записи хранения|Значение по умолчанию|Контейнер с именем `waimportexport`, который является контейнером по умолчанию. Например: <br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Ключ учетной записи хранения|Значение, заданное пользователем|Контейнер с именем, заданным пользователем. Например: <br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|SAS контейнера|Значение по умолчанию|Виртуальный каталог с именем по умолчанию (`waimportexport`) в контейнере, указанном в SAS.<br /><br /> Например, если для задания указан SAS `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, расположением журнала будет `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`.|  
|SAS контейнера|Значение, заданное пользователем|Виртуальный каталог с именем, заданным пользователем, в контейнере, указанном в SAS.<br /><br /> Например, если для задания указан SAS `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, а указанному виртуальному каталогу присвоено имя `mylogblobs`, расположение журнала будет `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
URL-адреса журнала ошибок и подробного журнала можно получить, вызвав операцию [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) (получить задание). Журналы будут доступны после завершения обработки диска.  
  
## <a name="log-file-format"></a>Формат файла журнала  
Формат обоих журналов одинаков: большой двоичный объект, содержащий XML-описания событий, возникших при копировании больших двоичных объектов между жестким диском и учетной записью клиента.  
  
Подробный журнал содержит полные сведения о состоянии операции копирования для каждого большого двоичного объекта (для задания импорта) или файла (для задания экспорта), а журнал ошибок содержит только сведения о больших двоичных объектах или файлах, в которых обнаружены ошибки при выполнении задания импорта или экспорта.  
  
Ниже приведен формат подробного журнала. Структура журнала ошибок такая же, но в нем отфильтрованы успешные операции.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

В следующей таблице описаны элементы файла журнала.  
  
|XML-элемент|type|ОПИСАНИЕ|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML-элемент|Представляет журнал диска.|  
|`Version`|Атрибут, строка|Версия формата журнала.|  
|`DriveId`|Строка|Серийный номер диска.|  
|`Status`|Строка|Состояние обработки диска. Дополнительные сведения см. в таблице `Drive Status Codes` ниже.|  
|`Blob`|Вложенный XML-элемент|Представляет большой двоичный объект.|  
|`Blob/BlobPath`|Строка|URI большого двоичного объекта.|  
|`Blob/FilePath`|Строка|Относительный путь к файлу на диске.|  
|`Blob/Snapshot`|Datetime|Версия моментального снимка большого двоичного объекта (только для задания экспорта).|  
|`Blob/Length`|Целое число |Общая длина большого двоичного объекта в байтах.|  
|`Blob/LastModified`|Datetime|Дата и время последнего изменения большого двоичного объекта (только для задания экспорта).|  
|`Blob/ImportDisposition`|Строка|Метод обработки импорта больших двоичных объектов (только для задания импорта).|  
|`Blob/ImportDisposition/@Status`|Атрибут, строка|Состояние метода обработки импорта.|  
|`PageRangeList`|Вложенный XML-элемент|Представляет список диапазонов страниц для страничного BLOB-объекта.|  
|`PageRange`|XML-элемент|Представляет диапазон страниц.|  
|`PageRange/@Offset`|Атрибут, целое число|Начальное смещение диапазона страниц в большом двоичном объекте.|  
|`PageRange/@Length`|Атрибут, целое число|Длина диапазона страниц в байтах.|  
|`PageRange/@Hash`|Атрибут, строка|MD5-хэш диапазона страниц в кодировке Base16.|  
|`PageRange/@Status`|Атрибут, строка|Состояние обработки диапазона страниц.|  
|`BlockList`|Вложенный XML-элемент|Представляет список блоков блочного BLOB-объекта.|  
|`Block`|XML-элемент|Представляет блок.|  
|`Block/@Offset`|Атрибут, целое число|Начальное смещение блока в большом двоичном объекте.|  
|`Block/@Length`|Атрибут, целое число|Длина блока в байтах.|  
|`Block/@Id`|Атрибут, строка|ИД блока.|  
|`Block/@Hash`|Атрибут, строка|MD5-хэш блока в кодировке Base16.|  
|`Block/@Status`|Атрибут, строка|Состояние обработки блока.|  
|`Metadata`|Вложенный XML-элемент|Представляет метаданные большого двоичного объекта.|  
|`Metadata/@Status`|Атрибут, строка|Состояние обработки метаданных большого двоичного объекта.|  
|`Metadata/GlobalPath`|Строка|Относительный путь к глобальному файлу метаданных.|  
|`Metadata/GlobalPath/@Hash`|Атрибут, строка|MD5-хэш глобального файла метаданных в кодировке Base16.|  
|`Metadata/Path`|Строка|Относительный путь к файлу метаданных.|  
|`Metadata/Path/@Hash`|Атрибут, строка|MD5-хэш файла метаданных в кодировке Base16.|  
|`Properties`|Вложенный XML-элемент|Представляет свойства большого двоичного объекта.|  
|`Properties/@Status`|Атрибут, строка|Состояние обработки свойств больших двоичных объектов, например "Файл не найден", "Завершена".|  
|`Properties/GlobalPath`|Строка|Относительный путь к глобальному файлу свойств.|  
|`Properties/GlobalPath/@Hash`|Атрибут, строка|MD5-хэш глобального файла свойств в кодировке Base16.|  
|`Properties/Path`|Строка|Относительный путь к файлу свойств.|  
|`Properties/Path/@Hash`|Атрибут, строка|MD5-хэш файла свойств в кодировке Base16.|  
|`Blob/Status`|Строка|Состояние обработки большого двоичного объекта.|  
  
# <a name="drive-status-codes"></a>Коды состояний диска  
В таблице ниже перечислены коды состояний обработки диска.  
  
|Код состояния|ОПИСАНИЕ|  
|-----------------|-----------------|  
|`Completed`|Обработка диска завершена без ошибок.|  
|`CompletedWithWarnings`|Обработка диска завершена с предупреждениями в одном или нескольких больших двоичных объектах в соответствии с методами обработки импорта, заданными для больших двоичных объектов.|  
|`CompletedWithErrors`|Обработка диска завершена с ошибками в одном или нескольких BLOB-объектах или блоках.|  
|`DiskNotFound`|Нет диска в дисководе.|  
|`VolumeNotNtfs`|Первый том данных на диске не в формате NTFS.|  
|`DiskOperationFailed`|Произошла неизвестная ошибка при выполнении операций на диске.|  
|`BitLockerVolumeNotFound`|Не найден том, зашифрованный с помощью BitLocker.|  
|`BitLockerNotActivated`|Средство BitLocker не включено в томе.|  
|`BitLockerProtectorNotFound`|Предохранитель ключа числового пароля не существует в этом томе.|  
|`BitLockerKeyInvalid`|Не удается разблокировать том с использованием указанного числового пароля.|  
|`BitLockerUnlockVolumeFailed`|Произошла неизвестная ошибка при попытке разблокировать том.|  
|`BitLockerFailed`|Произошла неизвестная ошибка при выполнении операций с BitLocker.|  
|`ManifestNameInvalid`|Недопустимое имя файла манифеста.|  
|`ManifestNameTooLong`|Имя файла манифеста слишком длинное.|  
|`ManifestNotFound`|Файл манифеста не найден.|  
|`ManifestAccessDenied`|Отказано в доступе к файлу манифеста.|  
|`ManifestCorrupted`|Файл манифеста поврежден (содержимое не совпадает с хэшем).|  
|`ManifestFormatInvalid`|Содержимое манифеста не соответствует необходимому формату.|  
|`ManifestDriveIdMismatch`|Идентификатор диска в файле манифеста не соответствует идентификатору, считанному с диска.|  
|`ReadManifestFailed`|Произошла ошибка дисковых операций ввода-вывода при считывании с манифеста.|  
|`BlobListFormatInvalid`|Большой двоичный объект списка больших двоичных объектов экспорта не соответствует необходимому формату.|  
|`BlobRequestForbidden`|Доступ к большим двоичным объектам в учетной записи хранения запрещен. Это может быть вызвано недопустимым ключом или SAS контейнера.|  
|`InternalError`|Произошла внутренняя ошибка при обработке диска.|  
  
## <a name="blob-status-codes"></a>Коды состояний большого двоичного объекта  
В таблице ниже перечислены коды состояний обработки большого двоичного объекта.  
  
|Код состояния|ОПИСАНИЕ|  
|-----------------|-----------------|  
|`Completed`|Обработка большого двоичного объекта завершена без ошибок.|  
|`CompletedWithErrors`|Обработка большого двоичного объекта завершена с ошибками в одном или нескольких диапазонах страниц, блоках, файлах метаданных или свойствах.|  
|`FileNameInvalid`|Недопустимое имя файла.|  
|`FileNameTooLong`|Имя файла слишком длинное.|  
|`FileNotFound`|Файл не найден.|  
|`FileAccessDenied`|Отказано в доступе к файлу.|  
|`BlobRequestFailed`|Не удалось выполнить запрос службы BLOB-объектов на доступ к большому двоичному объекту.|  
|`BlobRequestForbidden`|Запрос службы BLOB-объектов на доступ к большому двоичному объекту запрещен. Это может быть вызвано недопустимым ключом или SAS контейнера.|  
|`RenameFailed`|Не удалось переименовать большой двоичный объект (для задания импорта) или файл (для задания экспорта).|  
|`BlobUnexpectedChange`|Произошли непредвиденные изменения в большом двоичном объекте (для задания экспорта).|  
|`LeasePresent`|Имеется аренда на большой двоичный объект.|  
|`IOFailed`|Произошла ошибка дисковых или сетевых операций ввода-вывода при обработке большого двоичного объекта.|  
|`Failed`|Произошла неизвестная ошибка при обработке большого двоичного объекта.|  
  
## <a name="import-disposition-status-codes"></a>Коды состояний метода обработки импорта  
В следующей таблице перечислены коды состояний для устранения ошибок, связанных с методом обработки импорта.  
  
|Код состояния|ОПИСАНИЕ|  
|-----------------|-----------------|  
|`Created`|Создан большой двоичный объект.|  
|`Renamed`|Большой двоичный объект переименован в соответствии с методом обработки импорта rename. Элемент `Blob/BlobPath` содержит URI для переименованного большого двоичного объекта.|  
|`Skipped`|Большой двоичный объект пропущен в соответствии с методом обработки импорта `no-overwrite`.|  
|`Overwritten`|Имеющийся большой двоичный объект перезаписан большим двоичным объектом в соответствии с методом обработки импорта `overwrite`.|  
|`Cancelled`|В результате предыдущей ошибки приостановлена дальнейшая обработка метода обработки импорта.|  
  
## <a name="page-rangeblock-status-codes"></a>Коды состояний диапазона страниц или блока  
В таблице ниже перечислены коды состояний обработки диапазона страниц или блока.  
  
|Код состояния|ОПИСАНИЕ|  
|-----------------|-----------------|  
|`Completed`|Обработка диапазона страниц или блока завершена без ошибок.|  
|`Committed`|Блок зафиксирован, но не в полном списке блоков, так как возникли ошибки в остальных блоках или не удалось вставить полный список блокировок.|  
|`Uncommitted`|Блок отправлен, но не зафиксирован.|  
|`Corrupted`|Диапазон страниц или блок поврежден (содержимое не совпадает с хэшем).|  
|`FileUnexpectedEnd`|Обнаружен непредвиденный конец файла.|  
|`BlobUnexpectedEnd`|Обнаружен непредвиденный конец большого двоичного объекта.|  
|`BlobRequestFailed`|Не удалось выполнить запрос службы BLOB-объектов на доступ к диапазону страниц или блоку.|  
|`IOFailed`|Произошла ошибка дисковых или сетевых операций ввода-вывода при обработке диапазона страниц или блока.|  
|`Failed`|Произошла неизвестная ошибка при обработке диапазона страниц или блока.|  
|`Cancelled`|В результате предыдущей ошибки приостановлена дальнейшая обработка диапазона страниц или блока.|  
  
## <a name="metadata-status-codes"></a>Коды состояний метаданных  
В таблице ниже перечислены коды состояний обработки метаданных больших двоичных объектов.  
  
|Код состояния|ОПИСАНИЕ|  
|-----------------|-----------------|  
|`Completed`|Обработка метаданных завершена без ошибок.|  
|`FileNameInvalid`|Недопустимое имя файла метаданных.|  
|`FileNameTooLong`|Имя файла метаданных слишком длинное.|  
|`FileNotFound`|Файл метаданных не найден.|  
|`FileAccessDenied`|Отказано в доступе к файлу метаданных.|  
|`Corrupted`|Файл метаданных поврежден (содержимое не совпадает с хэшем).|  
|`XmlReadFailed`|Содержимое метаданных не соответствует необходимому формату.|  
|`XmlWriteFailed`|Не удалось выполнить запись XML-файла метаданных.|  
|`BlobRequestFailed`|Не удалось выполнить запрос службы BLOB-объектов на доступ к метаданным.|  
|`IOFailed`|Произошла ошибка дисковых или сетевых операций ввода-вывода при обработке метаданных.|  
|`Failed`|Произошла неизвестная ошибка при обработке метаданных.|  
|`Cancelled`|В результате предыдущей ошибки приостановлена дальнейшая обработка метаданных.|  
  
## <a name="properties-status-codes"></a>Коды состояний свойств  
В таблице ниже перечислены коды состояний обработки свойств больших двоичных объектов.  
  
|Код состояния|ОПИСАНИЕ|  
|-----------------|-----------------|  
|`Completed`|Обработка свойств завершена без ошибок.|  
|`FileNameInvalid`|Недопустимое имя файла свойств.|  
|`FileNameTooLong`|Имя файла свойств слишком длинное.|  
|`FileNotFound`|Файл свойств не найден.|  
|`FileAccessDenied`|Отказано в доступе к файлу свойств.|  
|`Corrupted`|Файл свойств поврежден (содержимое не совпадает с хэшем).|  
|`XmlReadFailed`|Содержимое файла свойств не соответствует необходимому формату.|  
|`XmlWriteFailed`|Не удалось выполнить запись XML-файла свойств.|  
|`BlobRequestFailed`|Не удалось выполнить запрос службы BLOB-объектов на доступ к свойствам.|  
|`IOFailed`|Произошла ошибка дисковых или сетевых операций ввода-вывода при обработке свойств.|  
|`Failed`|Произошла неизвестная ошибка при обработке свойств.|  
|`Cancelled`|В результате предыдущей ошибки приостановлена дальнейшая обработка свойств.|  
  
## <a name="sample-logs"></a>Примеры журналов  
Ниже приведен пример подробного журнала.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Далее показан соответствующий журнал ошибок.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 Следующий журнал ошибок задания импорта содержит ошибку о ненайденном файле на диске для импорта. Обратите внимание, что следующие компоненты находятся в состоянии `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

В следующем журнале ошибок задания экспорта указано, что содержимое большого двоичного объекта успешно записано на диск, но при экспорте свойств большого двоичного объекта произошла ошибка.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Дополнительная информация
 
* [Справочник по REST API импорта и экспорта службы хранилища](/rest/api/storageimportexport/)
