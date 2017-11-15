---
title: "Пример сценария Azure PowerShell и вычисление размера контейнера больших двоичных объектов | Документация Майкрософт"
description: "Сведения о вычислении размера контейнера в хранилище BLOB-объектов Azure за счет сложения размеров больших двоичных объектов, хранящихся в нем."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: cb053ba730a7dac5c23d98e1046fd63d27831e16
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="calculate-the-size-of-a-blob-container"></a>Вычисление размера контейнера больших двоичных объектов

Этот скрипт позволяет вычислить размер контейнера в хранилище BLOB-объектов Azure за счет сложения размеров больших двоичных объектов, хранящихся в нем.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="determine-the-size-of-the-blob-container"></a>Определение размера контейнера больших двоичных объектов

Общий размер контейнера больших двоичных объектов включает размер самого контейнера и размер всех больших двоичных объектов в контейнере.

В следующем разделе описывается, как вычисляется емкость хранилища для контейнеров больших двоичных объектов и больших двоичных объектов. В следующем разделе Len(X) означает число знаков в строке.

### <a name="blob-containers"></a>Контейнеры больших двоичных объектов

В следующем вычислении показано, как оценить объем хранилища, потребляемого контейнером больших двоичных объектов.

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

Ниже показан разбор:
* 48 байт нагрузки для каждого контейнера включают время последнего изменения, разрешения, общедоступные параметры и некоторые системные метаданные.

* Имя контейнера хранится в формате Юникода, поэтому умножьте количество знаков на два.

* Для всех сохраняемых блоков метаданных контейнера больших двоичных объектов сохраняется длина имени (в формате ASCII) и длина строкового значения.

* 512 байт каждого идентификатора подписи содержат имя подписанного идентификатора, время начала срока действия, время окончания срока действия и разрешения.

### <a name="blobs"></a>BLOB-объекты

В следующих вычислениях показано, как оценить объем хранилища, потребляемого для каждого большого двоичного объекта.

* Блочный BLOB-объект (базовый большой двоичный объект или моментальный снимок):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   `

* Страничный BLOB-объект (базовый большой двоичный объект или моментальный снимок):

   `
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   `

Ниже показан разбор:

* 124 байт нагрузки для большого двоичного объекта, который включает в себя:
    - время последнего изменения;
    - Размер
    - Cache-Control;
    - Content-Type
    - Content-Language;
    - Content-Encoding
    - Content-MD5;
    - Разрешения
    - сведения о моментальном снимке;
    - Lease
    - некоторые системные метаданные.

* Имя большого двоичного объекта хранится в формате Юникода, поэтому умножьте количество знаков на два.

* Для каждого блока сохраняемых метаданных сохраняется длина имени (в формате ASCII) и длина строкового значения.

* Для блочных BLOB-объектов:
    * 8 байт для списка блокировок;
    * количество блоков равно размеру идентификатора блока в байтах;
    * размер данных во всех зафиксированных и незафиксированных блоках. 
    
    >[!NOTE]
    >При использовании моментальных снимков этот размер включает только уникальные данные для этого базового большого двоичного объекта или большого двоичного объекта моментального снимка. Если незафиксированные блоки не используются в течение недели, они удаляются при сборке мусора. После этого они не учитываются при выставлении счетов.

* Для страничных BLOB-объектов:
    * Число непоследовательных диапазонов страниц данных равно 12 байтам. Это число уникальных диапазонов страницы, отображаемых при вызове API **GetPageRanges**.

    * Объем данных в байтах для всех сохраненных страниц. 
    
    >[!NOTE]
    >При использовании моментальных снимков этот размер включает только учитываемые уникальные страницы для базового большого двоичного объекта или большого двоичного объекта моментального снимка.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о выставлении счетов в службе хранилища Azure см. в статье [Understanding Windows Azure Storage Billing – Bandwidth, Transactions, and Capacity](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/) (Общие сведения о выставлении счетов в службе хранилища Azure. Пропускная способность, транзакции и емкость).

- Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.4.1).

- Дополнительные примеры сценариев PowerShell хранилища см. в статье [Примеры Azure PowerShell для хранилища BLOB-объектов Azure](../blobs/storage-samples-blobs-powershell.md).
