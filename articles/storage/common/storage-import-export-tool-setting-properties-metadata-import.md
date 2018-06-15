---
title: Настройка свойств и метаданных с помощью службы импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как указать свойства и метаданные, используемые для настройки целевых больших двоичных объектов, при запуске инструмента импорта и экспорта Azure для подготовки дисков.
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
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1ba6d157402fae0c7d7bf841d2b4e4f6b1ee1084
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059409"
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
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Сохраните файл в локальном расположении, например `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Добавьте путь к файлам свойств и метаданных в dataset.csv.

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Дополнительная информация

* [Формат файла свойств и метаданных службы импорта и экспорта](../storage-import-export-file-format-metadata-and-properties.md)
