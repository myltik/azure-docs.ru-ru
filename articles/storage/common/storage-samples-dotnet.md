---
title: Примеры для службы хранилища Azure с использованием .NET | Документация Майкрософт
description: Просмотрите, загрузите и запустите образцы кода и приложений для хранилища Azure. Воспользуйтесь примерами для начала работы с большими двоичными объектами, очередями, таблицами и файлами с помощью клиентских библиотек хранилища .NET.
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
ms.openlocfilehash: 1e6973f0decc448657d869afb8823dd03c62d272
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32191253"
---
# <a name="azure-storage-samples-using-net"></a>Примеры для службы хранилища Azure с использованием .NET

## <a name="net-sample-index"></a>Указатель по примерам для .NET

В таблице ниже приведен обзор репозитория примеров и сценарии, описанные в каждом примере. Щелкните ссылки для просмотра соответствующего примера кода на сайте GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Конечная точка</th><th style="font-size:110%">Сценарий</th><th style="font-size:110%">Пример кода</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Большой двоичный объект</b></td>
<td>Добавление больших двоичных объектов</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">Пример метода CloudBlobContainer.GetAppendBlobReference</a></td> 
</tr> 
<tr> 
<td>Блочный BLOB-объект</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Веб-приложение фотоальбома для хранилища BLOB-объектов Azure</a></td>
</tr> 
<tr> 
<td>Шифрование на стороне клиента</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">Примеры шифрования больших двоичных объектов</a></td>
</tr> 
<tr> 
<td>Копирование BLOB-объекта</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr> 
<tr> 
<td>Create Container (Создание контейнера)</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Веб-приложение фотоальбома для хранилища BLOB-объектов Azure</a></td>
</tr> 
<tr> 
<td>Delete BLOB (Удаление BLOB-объекта)</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Веб-приложение фотоальбома для хранилища BLOB-объектов Azure</a></td>
</tr> 
<tr> 
<td>Delete Container (Удаление контейнера)</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr> 
<tr> 
<td>Метаданные, свойства и статистика больших двоичных объектов</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr> 
<tr> 
<td>ACL, метаданные и свойства контейнера</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Веб-приложение фотоальбома для хранилища BLOB-объектов Azure</a></td>
</tr> 
<tr> 
<td>Get Page Ranges (Получение диапазона страницы)</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr> 
<tr> 
<td>Аренда большого двоичного объекта и контейнера</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr> 
<tr> 
<td>Список больших двоичных объектов и контейнеров</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr> 
<tr> 
<td>Страничный BLOB-объект</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr>   
<tr> 
<td>Свойства службы</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">Приступая к работе с большими двоичными объектами</a></td>
</tr>           
<tr> 
<td>Создание моментального снимка большого двоичного объекта</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Backup Azure Virtual Machine Disks with Incremental Snapshots</a> (Резервное копирование дисков виртуальной машины Azure с помощью добавочных моментальных снимков)</td>
</tr> 
<tr> 
<td rowspan="9"><b>File</b></td>
<td>Создание общих папок, каталогов и файлов</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr>
<tr> 
<td>Удаление общих папок, каталогов и файлов</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">Приступая к работе со службой файлов Azure в .NET</a></td> 
</tr> 
<tr> 
<td>Метаданные и свойства каталога</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr> 
<tr> 
<td>Скачивание файлов</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr> 
<tr> 
<td>Метрики, метаданные и свойства файла</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr> 
<tr> 
<td>Свойства службы файлов</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr> 
<tr> 
<td>Список каталогов и файлов</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr>
<tr> 
<td>Список общих папок</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr>
<tr> 
<td>Статистика, метаданные и свойства общей папки</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Пример хранилища файлов .NET для службы хранилища Azure</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Очередь</b></td>
<td>Добавление сообщения</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Приступая к работе со службой очередей Azure в .NET</a></td> 
</tr> 
<tr> 
<td>Шифрование на стороне клиента</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Очередь шифрования на стороне клиента .NET для службы хранилища Azure</a></td> 
</tr> 
<tr> 
<td>Создание очередей</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Приступая к работе со службой очередей Azure в .NET</a></td> 
</tr> 
<tr> 
<td>Удаление сообщения и очереди</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Приступая к работе со службой очередей Azure в .NET</a></td> 
</tr> 
<tr> 
<td>Просмотр сообщения</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Приступая к работе со службой очередей Azure в .NET</a></td> 
</tr> 
<tr> 
<td>ACL, метаданные и статистика очереди</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Приступая к работе со службой очередей Azure в .NET</a></td> 
</tr> 
<tr> 
<td>Свойства службы очередей</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">Приступая к работе со службой очередей Azure в .NET</a></td> 
</tr> 
<tr> 
<td>Сообщение об обновлении</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">Приступая к работе со службой очередей Azure в .NET</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Таблица</b></td>
<td>Создание таблицы</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Управление параллелизмом с помощью службы хранилища Azure: пример приложения</a></td> 
</tr> 
<tr> 
<td>Удаление сущности или таблицы</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Приступая к работе с хранилищем таблиц Azure в .NET)</td> 
</tr> 
<tr> 
<td>Вставка, слияние или замена сущностей</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Управление параллелизмом с помощью службы хранилища Azure: пример приложения</a></td> 
</tr> 
<tr> 
<td>Query Entities (Сущности запроса)</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Приступая к работе с хранилищем таблиц Azure в .NET)</td> 
</tr> 
<tr> 
<td>Запросы к таблицам</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Приступая к работе с хранилищем таблиц Azure в .NET)</td> 
</tr> 
<tr> 
<td>Свойства и ACL таблицы</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">Getting Started with Azure Table Storage in .NET</a> (Приступая к работе с хранилищем таблиц Azure в .NET)</td> 
</tr> 
<tr> 
<td>Update Entity (Обновление сущности)</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Управление параллелизмом с помощью службы хранилища Azure: пример приложения</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Библиотека примеров кода Azure

Чтобы просмотреть полную библиотеку примеров, перейдите в [библиотеку примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=storage), включающую примеры для службы хранилища Azure, которые можно скачать и запустить локально. Пример библиотеки кода содержит пример кода в формате ZIP. Кроме того, можно просмотреть и клонировать репозиторий GitHub для каждого примера.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>Руководства по началу работы

Ознакомьтесь со следующими руководствами, если вам нужны инструкции по установке клиентских библиотек службы хранилища Azure и началу работы с ними.

* [Приступая к работе со службой BLOB-объектов Azure в .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Приступая к работе со службой очередей Azure в .NET](../storage-dotnet-how-to-use-queues.md)
* [Приступая к работе со службой таблиц Azure в .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Приступая к работе со службой файлов Azure в .NET](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о примерах для других языков см. здесь:

* Java: [Azure Storage samples using Java](storage-samples-java.md) (Примеры для службы хранилища Azure с использованием Java)
* Все остальные языки: [Azure Storage samples](../storage-samples.md) (Примеры для службы хранилища Azure)
