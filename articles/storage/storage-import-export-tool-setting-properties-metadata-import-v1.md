---
title: "Настройка свойств и метаданных во время импорта | Документация Майкрософт"
description: "Сведения об указании свойств и метаданных, используемых для настройки целевых больших двоичных объектов, при запуске инструмента импорта и экспорта для подготовки дисков."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 5d75bdb5ff39934e02d9af721b51e0aca24026bb
ms.openlocfilehash: f49604cf6738da3576ed2a65ca96d083d5e8219b


---

# <a name="setting-properties-and-metadata-during-the-import-process"></a>Настройка свойств и метаданных во время импорта
При запуске инструмента импорта и экспорта Microsoft Azure для подготовки дисков вы можете указать свойства и метаданные, предназначенные для настройки целевых больших двоичных объектов. Выполните следующие действия.  
  
1.  Чтобы задать свойства больших двоичных объектов, создайте текстовый файл на локальном компьютере, определяющий имена и значения свойств.  
  
2.  Чтобы задать метаданные больших двоичных объектов, создайте текстовый файл на локальном компьютере, определяющий имена и значения метаданных.  
  
3.  Передайте полный путь к одну или обоим файлам в инструмент импорта и экспорта Azure в ходе операции `PrepImport`.  
  
> [!NOTE]
>  При указании файла свойств или метаданных в рамках сеанса копирования они задаются для каждого большого двоичного объекта, импортируемого в рамках этого сеанса копирования. Если вы хотите задать другой набор свойств или метаданных для определенных импортируемых больших двоичных объектов, необходимо создать отдельный сеанс копирования с другими файлами свойств и метаданных.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Указание свойств большого двоичного объекта в текстовом файле  
Чтобы задать свойства больших двоичных объектов, создайте локальный текстовый файл и добавьте элемент XML, определяющий имена свойств в качестве элементов, а значения свойств в качестве значений. Ниже приведен пример указания некоторых значений свойств.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Сохраните файл в локальном расположении, например `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Указание метаданных большого двоичного объекта в текстовом файле  
Аналогичным образом, чтобы задать метаданные больших двоичных объектов, создайте локальный текстовый файл, определяющий имена метаданных в качестве элементов и значения метаданных в качестве значений. Ниже приведен пример указания некоторых значений метаданных.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Сохраните файл в локальном расположении, например `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Создание сеанса копирования, содержащего файлы свойств и метаданных  
При запуске инструмента импорта и экспорта Azure для подготовки задания импорта укажите в командной строке файл свойств, используя параметр `PropertyFile`. Для указания файла метаданных используйте параметр `/MetadataFile`. Ниже приведен пример указания обоих файлов.  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="see-also"></a>См. также  
[Формат файла свойств и файла метаданных службы импорта и экспорта](storage-import-export-file-format-metadata-and-properties.md)



<!--HONumber=Dec16_HO2-->


