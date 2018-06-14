---
title: Пример рабочего процесса подготовки жестких дисков для задания импорта инструмента импорта и экспорта Azure | Документация Майкрософт
description: Ознакомьтесь с пошаговым руководством, чтобы изучить полный процесс подготовки дисков для задания импорта в службе импорта и экспорта Azure.
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
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059539"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Пример рабочего процесса подготовки жестких дисков для задания импорта

В этой статье рассматривается полный процесс подготовки дисков для задания импорта.

## <a name="sample-data"></a>Пример данных

Этот пример импортирует в учетную запись хранения Azure `mystorageaccount` приведенные ниже данные.

|Расположение|ОПИСАНИЕ|Размер данных|
|--------------|-----------------|-----|
|H:\Video\ |Коллекция видео.|12 ТБ|
|H:\Photo\ |Коллекция фотографий.|30 ГБ|
|K:\Temp\FavoriteMovie.ISO|Образ диска Blu-Ray™.|25 ГБ|
|\\\bigshare\john\music\ |Коллекция музыкальных файлов в сетевой папке.|10 ГБ|

## <a name="storage-account-destinations"></a>Целевые каталоги в учетной записи хранения

Задание импорта импортирует данные в следующие расположения в учетной записи хранения.

|Источник|Целевой виртуальный каталог или большой двоичный объект.|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

При таком сопоставлении файл `H:\Video\Drama\GreatMovie.mov` будет импортирован в большой двоичный объект `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Определение требований к жестким дискам

Далее, чтобы определить, сколько жестких дисков нужно, можно вычислить размер данных.

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Для нашего примера будет достаточно двух жестких дисков объемом в 8 ТБ каждый. Однако, так как исходный каталог `H:\Video` содержит 12 ТБ данных, а емкость одного жесткого диска составляет 8 ТБ, вы сможете указать это в файле **driveset.csv**, как показано ниже.

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Средство оптимальным образом распределит данные по двум жестким дискам.

## <a name="attach-drives-and-configure-the-job"></a>Подключение дисков и настройка задания
Следует подключить оба диска к компьютеру и создать на них тома. Затем создайте файл **dataset.csv**:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Кроме того, для всех файлов можно задать следующие метаданные.

* **UploadMethod:** Microsoft Azure Import/Export service
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Чтобы задать метаданные для импортируемых файлов, создайте текстовый файл `c:\WAImportExport\SampleMetadata.txt` со следующим содержимым.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Можно также задать некоторые свойства большого двоичного объекта `FavoriteMovie.ISO`.

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Чтобы задать эти свойства, создайте текстовый файл `c:\WAImportExport\SampleProperties.txt`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Запуск инструмента импорта и экспорта Azure (WAImportExport.exe)

Теперь все готово к запуску инструмента импорта и экспорта Azure для подготовки двух жестких дисков.

**Для первого сеанса:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Если нужно добавить какие-либо дополнительные данные, создайте другой файл набора данных (в том же формате, что и первоначальный набор данных).

**Для второго сеанса:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

После того, как сеансы копирования будут завершены, можно будет отключить оба диска от компьютера копирования и доставить их в соответствующий центр обработки данных Azure. При создании задания импорта на портале Azure вы передадите два файла журнала, `<FirstDriveSerialNumber>.xml` и `<SecondDriveSerialNumber>.xml`.

## <a name="next-steps"></a>Дополнительная информация

* [Подготовка жестких дисков для задания импорта](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Краткий справочник по часто используемым командам](../storage-import-export-tool-quick-reference.md)
