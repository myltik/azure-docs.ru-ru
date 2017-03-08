---
title: "Примеры для службы хранилища Azure с использованием Java | Документация Майкрософт"
description: "Просмотрите, загрузите и запустите образцы кода и приложений для хранилища Azure. Воспользуйтесь примерами для начала работы с большими двоичными объектами, очередями, таблицами и файлами с помощью клиентских библиотек хранилища Java."
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 01/12/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: ae5719c60c6ed2d3467f162f3b273d5bfdcf0073
ms.openlocfilehash: 9a311233a6e604ccdb6ab862f11774bff7382a8d


---
# <a name="azure-storage-samples-using-java"></a>Примеры для службы хранилища Azure с использованием Java

## <a name="java-sample-index"></a>Указатель по примерам для Java

В таблице ниже приведен обзор репозитория примеров и сценарии, описанные в каждом примере. Щелкните ссылки для просмотра соответствующего кода примера в GitHub.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">Конечная точка</th><th style="font-size:110%">Сценарий</th><th style="font-size:110%">Пример кода</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>BLOB-объект</b></td>
<td>Добавление больших двоичных объектов</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Блочный BLOB-объект</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Шифрование на стороне клиента</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Приступая к работе с шифрованием на стороне клиента Azure в Java</a></td>
</tr> 
<tr> 
<td>Копирование BLOB-объекта</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Create Container (Создание контейнера)</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Delete BLOB (Удаление BLOB-объекта)</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Delete Container (Удаление контейнера)</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Метаданные, свойства и статистика больших двоичных объектов</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>ACL, метаданные и свойства контейнера</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Get Page Ranges (Получение диапазона страницы)</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">Пример тестов страничного BLOB-объекта</a></td>
</tr> 
<tr> 
<td>Аренда большого двоичного объекта и контейнера</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Список больших двоичных объектов и контейнеров</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td>Страничный BLOB-объект</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/SasTests.java">Пример тестов SAS</a></td>
</tr>   
<tr> 
<td>Свойства службы</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr>           
<tr> 
<td>Создание моментального снимка большого двоичного объекта</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Приступая к работе со службой BLOB-объектов Azure на языке Java</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>Файл</b></td>
<td>Создание общих папок, каталогов и файлов</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr>
<tr> 
<td>Удаление общих папок, каталогов и файлов</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr> 
<tr> 
<td>Метаданные и свойства каталога</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr> 
<tr> 
<td>Скачивание файлов</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr> 
<tr> 
<td>Метрики, метаданные и свойства файла</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr> 
<tr> 
<td>Свойства службы файлов</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr> 
<tr> 
<td>Список каталогов и файлов</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr>
<tr> 
<td>Список общих папок</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr>
<tr> 
<td>Статистика, метаданные и свойства общей папки</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Приступая к работе со службой файлов Azure в Java</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>Очередь</b></td>
<td>Добавление сообщения</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Примеры для клиентской библиотеки хранилища Java</a></td> 
</tr> 
<tr> 
<td>Шифрование на стороне клиента</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Примеры для клиентской библиотеки хранилища Java</a></td> 
</tr> 
<tr> 
<td>Создание очередей</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Приступая к работе со службой очередей Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Удаление сообщения и очереди</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Приступая к работе со службой очередей Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Просмотр сообщения</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Приступая к работе со службой очередей Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>ACL, метаданные и статистика очереди</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Приступая к работе со службой очередей Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Свойства службы очередей</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Приступая к работе со службой очередей Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Сообщение об обновлении</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Приступая к работе со службой очередей Azure на языке Java</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>Таблица</b></td>
<td>Создание таблицы</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Приступая к работе со службой таблиц Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Удаление сущности или таблицы</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Приступая к работе со службой таблиц Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Вставка, слияние или замена сущностей</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Примеры для клиентской библиотеки хранилища Java</a></td> 
</tr> 
<tr> 
<td>Query Entities (Сущности запроса)</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Приступая к работе со службой таблиц Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Запросы к таблицам</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableBasics.java">Приступая к работе со службой таблиц Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Свойства и ACL таблицы</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/TableAdvanced.java">Приступая к работе со службой таблиц Azure на языке Java</a></td> 
</tr> 
<tr> 
<td>Update Entity (Обновление сущности)</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/table/gettingtstarted/TableBasics.java">Примеры для клиентской библиотеки хранилища Java</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Библиотека примеров кода Azure

Чтобы просмотреть полную библиотеку примеров, перейдите в [библиотеку примеров кода Azure](https://azure.microsoft.com/resources/samples/?service=storage), включающую примеры для службы хранилища Azure, которые можно скачать и запустить локально. Пример библиотеки кода содержит пример кода в формате ZIP. Кроме того, можно просмотреть и клонировать репозиторий GitHub для каждого примера.

[!INCLUDE [storage-java-samples-include](../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>Руководства по началу работы

Ознакомьтесь со следующими руководствами, если вам нужны инструкции по установке клиентских библиотек службы хранилища Azure и началу работы с ними.

* [Getting Started with Azure Blob Service in Java](storage-java-how-to-use-blob-storage.md) (Приступая к работе со службой BLOB-объектов Azure на языке Java)
* [Getting Started with Azure Queue Service in Java](storage-java-how-to-use-queue-storage.md) (Приступая к работе со службой очередей Azure на языке Java)
* [Getting Started with Azure Table Service in Java](storage-java-how-to-use-table-storage.md) (Приступая к работе со службой таблиц Azure на языке Java)
* [Getting Started with Azure File Service in Java](storage-java-how-to-use-file-storage.md) (Приступая к работе с файловой службой Azure на языке Java)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о примерах для других языков см. здесь:

* .NET: [Azure Storage samples using .NET](storage-samples-dotnet.md) (Примеры для службы хранилища Azure с использованием .NET)
* Все остальные языки: [Azure Storage samples](storage-samples.md) (Примеры для службы хранилища Azure)


<!--HONumber=Jan17_HO2-->


