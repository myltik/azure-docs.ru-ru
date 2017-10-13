---
title: "Формат файла свойств и файла метаданных импорта и экспорта Azure | Документация Майкрософт"
description: "Узнайте, как указать метаданные и свойства для одного или нескольких больших двоичных объектов, входящих в задание импорта или экспорта."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Формат файла свойств и файла метаданных службы импорта и экспорта Azure
В рамках задания импорта или экспорта можно указать метаданные и свойства для одного или нескольких больших двоичных объектов. Чтобы задать метаданные и свойства для больших двоичных объектов, созданных в рамках задания импорта, на жестком диске необходимо создать файл метаданных или файл свойств, содержащий импортируемые данные. Для задания экспорта метаданные и свойства записываются в файл метаданных или файл свойств, содержащийся на жестком диске, возвращаемом пользователю.  
  
## <a name="metadata-file-format"></a>Формат файла метаданных  
Файл метаданных имеет следующий формат:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML-элемент|Тип|Описание|  
|-----------------|----------|-----------------|  
|`Metadata`|Корневой элемент|Корневой элемент файла метаданных.|  
|`metadata-name`|Строка|необязательный параметр. XML-элемент указывает имя метаданных большого двоичного объекта, а его значение определяет значение параметра метаданных.|  
  
## <a name="properties-file-format"></a>Формат файла свойств  
Файл свойств имеет следующий формат:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML-элемент|Тип|Описание|  
|-----------------|----------|-----------------|  
|`Properties`|Корневой элемент|Корневой элемент файла свойств.|  
|`Last-Modified`|Строка|необязательный параметр. Время последнего изменения большого двоичного объекта. Только для заданий экспорта.|  
|`Etag`|Строка|необязательный параметр. Значение ETag большого двоичного объекта. Только для заданий экспорта.|  
|`Content-Length`|Строка|необязательный параметр. Размер большого двоичного объекта в байтах. Только для заданий экспорта.|  
|`Content-Type`|Строка|необязательный параметр. Тип содержимого большого двоичного объекта|  
|`Content-MD5`|Строка|необязательный параметр. Хэш MD5 большого двоичного объекта.|  
|`Content-Encoding`|Строка|необязательный параметр. Кодирование содержимого большого двоичного объекта.|  
|`Content-Language`|string|необязательный параметр. Язык содержимого большого двоичного объекта.|  
|`Cache-Control`|Строка|необязательный параметр. Строка управления кэшем для большого двоичного объекта.|  

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные правила настройки свойств и метаданных больших двоичных объектов см. в статьях [Set Blob Properties](/rest/api/storageservices/set-blob-properties) (Задание свойств большого двоичного объекта), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) (Задание метаданных большого двоичного объекта) и [Setting and Retrieving Properties and Metadata for Blob Resources](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) (Задание и получение свойств и метаданных для ресурсов больших двоичных объектов).
