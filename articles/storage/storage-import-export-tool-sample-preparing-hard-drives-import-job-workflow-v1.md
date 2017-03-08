---
title: "Пример рабочего процесса подготовки жестких дисков для задания импорта | Документация Майкрософт"
description: "Ознакомьтесь с пошаговым руководством, чтобы изучить полный процесс подготовки дисков для задания импорта в службе импорта и экспорта Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: ee7a8c9ae4cda5b67184100dd37ee4e0384aff26
ms.lasthandoff: 02/16/2017


---

# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Пример рабочего процесса подготовки жестких дисков для задания импорта
В этом разделе рассматривается полный процесс подготовки дисков для задания импорта.  
  
Этот пример импортирует в учетную запись хранения Microsoft Azure `mystorageaccount` приведенные ниже данные.  
  
|Расположение|Описание|  
|--------------|-----------------|  
|H:\Video|Коллекция видео, общий размер которой составляет 5 ТБ.|  
|H:\Photo|Коллекция фотографий, общий размер которой составляет 30 ГБ.|  
|K:\Temp\FavoriteMovie.ISO|Образ диска Blu-Ray™, размер которого составляет 25 ГБ.|  
|\\\bigshare\john\music|Коллекция музыкальных файлов в сетевой папке, общий размер которой составляет 10 ГБ.|  
  
Задание импорта импортирует эти данные в следующие расположения в учетной записи хранения.  
  
|Источник|Целевой виртуальный каталог или большой двоичный объект.|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
При таком сопоставлении файл `H:\Video\Drama\GreatMovie.mov` будет импортирован в большой двоичный объект `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Далее, чтобы определить, сколько жестких дисков нужно, можно вычислить размер данных.  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Для нашего примера будет достаточно двух жестких дисков объемом в 3 ТБ каждый. Однако, так как исходный каталог `H:\Video` содержит 5 ТБ данных, а емкость одного жесткого диска составляет 3 ТБ, прежде чем запустить средство импорта и экспорта Microsoft Azure, необходимо разбить `H:\Video` на два каталога меньшего размера: `H:\Video1` и `H:\Video2`. На этом шаге создаются приведенные ниже исходные каталоги.  
  
|Расположение|Размер|Целевой виртуальный каталог или большой двоичный объект.|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 ТБ|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 ТБ|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 ГБ|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 ГБ|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 ГБ|https://mystorageaccount.blob.core.windows.net/music|  
  
 Обратите внимание, что хотя каталог `H:\Video` разбит на два каталога, они оба указывают на один целевой виртуальный каталог в учетной записи хранения. Таким образом все видеофайлы содержатся в одном контейнере `video` в учетной записи хранения.  
  
 Далее перечисленные выше исходные каталоги равномерно распределяются по двум жестким дискам.  
  
||||  
|-|-|-|  
|Жесткий диск|Исходные каталоги|Общий размер|  
|Первый диск|H:\Video1|2,5 ТБ + 30 ГБ|  
||H:\Photo||  
|Второй диск|H:\Video2|2,5 ТБ + 35 ГБ|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Кроме того, для всех файлов можно задать следующие метаданные.  
  
-   **UploadMethod:** Microsoft Azure Import/Export Service  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
Чтобы задать метаданные для импортируемых файлов, создайте текстовый файл `c:\WAImportExport\SampleMetadata.txt` со следующим содержимым.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Можно также задать некоторые свойства большого двоичного объекта `FavoriteMovie.ISO`.  
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Чтобы задать эти свойства, создайте текстовый файл `c:\WAImportExport\SampleProperties.txt`.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Теперь все готово к запуску средства импорта и экспорта Azure для подготовки двух жестких дисков. Обратите внимание на следующее.  
  
-   Первый диск подключается как диск X.  
  
-   Второй диск подключается как диск Y.  
  
-   Ключ для учетной записи хранения `mystorageaccount`: `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Подготовка диска для импорта, когда данные предварительно загружены
 
 Если данные для импорта уже находятся на диске, используйте флаг /skipwrite. Значения /t и /srcdir должны указывать на диск, подготавливаемый для импорта. Если не все данные на диске нужно переместить в один целевой виртуальный каталог или корневой каталог учетной записи хранения, повторите команду отдельно для каждого каталога, не меняя при этом значение /id.

>[!NOTE] 
>Не указывайте флаг /format, так как он вызовет очистку данных с диска. Можно указать флаг /encrypt или /bk, в зависимости от того, зашифрован диск или нет. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

Для первого диска дважды запустите средство импорта и экспорта Azure, чтобы скопировать два исходных каталога.  
  
```
## First copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

```  
## Second copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```
  
Для второго диска трижды запустите средство импорта и экспорта Azure: по одному разу для каждого исходного каталога и один раз для изолированного файла образа Blu-Ray™.  
  
```
## First copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
```
## Second copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
```
## Third copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
После того, как сеансы копирования будут завершены, можно будет отключить оба диска от компьютера копирования и доставить их в соответствующий центр обработки данных Microsoft Azure. При создании задания импорта на [портале управления Microsoft Azure](https://manage.windowsazure.com/) вы передадите два файла журнала, `FirstDrive.jrn` и `SecondDrive.jrn`.  
  
## <a name="see-also"></a>См. также  
[Подготовка жестких дисков для задания импорта](storage-import-export-tool-preparing-hard-drives-import-v1.md)   
[Краткий справочник по часто используемым командам](storage-import-export-tool-quick-reference-v1.md) 

